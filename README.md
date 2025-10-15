# Go-Trust Comprehensive Test Infrastructure

This directory contains a complete test infrastructure for the Go-Trust system, including realistic trust service lists (TSLs), certificate authorities, and integration tests.

## Directory Structure

```
comprehensive-test/
├── README.md                              # This file
├── comprehensive-test-pipeline.yaml       # Pipeline configuration for testing
├── comprehensive_integration_test.go      # Integration test (located in pkg/pipeline/)
├── test-trust-infrastructure/            # Complete test trust infrastructure
│   ├── lotl/                            # List of Trusted Lists (LOTL)
│   │   ├── scheme.yaml                  # LOTL configuration
│   │   └── providers/                   # National TSL providers
│   │       ├── sweden/                  # Swedish national TSL
│   │       └── germany/                 # German national TSL
│   ├── swedish-tsl/                     # Swedish TSL with Swedbank CA
│   │   ├── scheme.yaml                  # Swedish scheme configuration
│   │   └── providers/
│   │       └── swedbank/                # Swedbank trust service provider
│   └── german-tsl/                      # German TSL with Deutsche Telekom CA
│       ├── scheme.yaml                  # German scheme configuration
│       └── providers/
│           └── deutsche-telekom/        # Deutsche Telekom trust service provider
├── test-certificates/                    # Test certificate infrastructure
│   ├── swedbank-root-ca.pem            # Swedbank root CA certificate
│   ├── telekom-root-ca.pem             # Deutsche Telekom root CA certificate
│   └── test-client-cert.pem            # Test client certificate for validation
└── test-authzen/                        # AuthZEN API test files
    ├── valid-cert-request.json         # Valid certificate validation request
    ├── invalid-cert-request.json       # Invalid certificate validation request
    └── malformed-request.json          # Malformed request for error testing
```

## Key Components

### Trust Infrastructure
- **LOTL (List of Trusted Lists)**: Multi-national trust infrastructure with Swedish and German national TSLs
- **Swedish TSL**: Contains Swedbank as a trust service provider with real certificate chain
- **German TSL**: Contains Deutsche Telekom as a trust service provider with real certificate chain

### Test Pipeline
- **comprehensive-test-pipeline.yaml**: Complete pipeline configuration that:
  1. Generates TSLs from the test trust infrastructure
  2. Publishes XML files to output directory
  3. Loads and validates certificate pools
  4. Transforms TSLs to HTML format

### Integration Testing
- **comprehensive_integration_test.go**: Full integration test that validates:
  - TSL generation from directory structures
  - Certificate pool creation and validation
  - XML publishing functionality
  - End-to-end pipeline execution

## Running Tests

### Integration Test
```bash
cd pkg/pipeline
go test -v -run TestComprehensiveIntegration
```

### All Pipeline Tests
```bash
cd pkg/pipeline
go test -v ./...
```

### With Coverage
```bash
go test -coverprofile=cover.out ./...
go tool cover -func=cover.out
```

## Pipeline Execution

### Run Test Pipeline
```bash
cd comprehensive-test
../gt -pipeline comprehensive-test-pipeline.yaml
```

### View Generated Output
```bash
ls -la output/        # Generated TSL XML files
ls -la html-output/   # Transformed HTML files (if transform step enabled)
```

## Certificate Details

### Swedbank Root CA
- **Subject**: CN=Swedbank Root CA, O=Swedbank AB, C=SE
- **Usage**: Root certificate authority for Swedish financial services
- **Format**: PEM encoded X.509 certificate
- **Key Usage**: Digital Signature, Certificate Signing, CRL Signing

### Deutsche Telekom Root CA
- **Subject**: CN=Deutsche Telekom Root CA 2, O=Deutsche Telekom AG, C=DE
- **Usage**: Root certificate authority for German telecommunications
- **Format**: PEM encoded X.509 certificate
- **Key Usage**: Digital Signature, Certificate Signing, CRL Signing

## AuthZEN Integration

The test infrastructure includes sample AuthZEN requests for testing certificate validation:

### Valid Request
Tests successful certificate validation against loaded trust pools.

### Invalid Request
Tests rejection of certificates not in the trust infrastructure.

### Malformed Request
Tests error handling for improperly formatted requests.

## Technical Notes

### Certificate Encoding Fix
This test infrastructure helped identify and fix a critical bug in `pkg/pipeline/steps.go` where TSL generation was incorrectly encoding PEM certificates instead of raw DER bytes. The fix ensures certificates are properly loaded into Go's x509.CertPool.

### Performance
The integration test typically completes in under 1 second and validates:
- 3 TSL generations (LOTL, Swedish, German)
- Certificate pool loading for all TSLs
- XML file publishing
- Memory efficiency of certificate storage

### Coverage
The comprehensive test infrastructure contributes to 73.5% test coverage across the pipeline package, ensuring robust validation of all major code paths.

## Next Steps

Potential enhancements for this test infrastructure:

1. **Digital Signing**: Add XML-DSIG signatures to generated TSLs using Go-Trust's signing capabilities
2. **HSM Integration**: Extend tests to cover PKCS#11 hardware security module integration
3. **Signature Verification**: Implement and test the verify pipeline step for signature validation
4. **Performance Testing**: Add benchmark tests for large-scale TSL processing
5. **Network Testing**: Add tests for HTTP/HTTPS TSL fetching scenarios
