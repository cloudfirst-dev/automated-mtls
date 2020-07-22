# End Client
Execute curl to the product service that is unsecure to products and mtls between products and rating services
1. Get route endpoint and set to an environment variable
    ```
    export ROUTE_HOST=$(oc get route product -o "jsonpath={.spec.host}")
    ```
1. Get the product and its votes
    ```
    curl http://$ROUTE_HOST/product/1
    ```
1. You should see the following
    ```
    {"id":1,"productName":"Test Product","votes":0}
    ```

# API Gateway mTLS
Execute curl (acting as an api gateway) to demonstrate securing a backend service with mTLS
1. Get route endpoint and set to an environment variable
    ```
    export RATING_HOST=$(oc get route rating -o "jsonpath={.spec.host}")
    ```
1. Get the ca and api gateway cert generated with cert manager and the self signed ca
    ```
    oc get secret api-gateway-example-com-tls -o "jsonpath={.data['ca\.crt']}" | base64 -D > ca.crt
    ```
1. Get the private key for the api-gateway
    ```
    oc get secret api-gateway-example-com-tls -o "jsonpath={.data['tls\.key']}" | base64 -D > tls.key
    ```
1. Get the public cert for the api-gateway
    ```
    oc get secret api-gateway-example-com-tls -o "jsonpath={.data['tls\.crt']}" | base64 -D > tls.crt
    ```
1. Execute the request with api-gateway cert
    ```
    curl --cacert ca.crt \
        --key tls.key \
        --cert tls.crt \
        --insecure \
        https://$RATING_HOST/rating/1
    ```