# Currency Converter UI
Web UI written in vuejs that communicates to http://127.0.0.1:8000 to retrieve currency rates.

Rate is shown everytime there's a change in any input field.

## Project setup
```shell
# build docker image
docker build . -t ui/currency-conversion

# run docker container
docker run -it -p 8080:8080 --rm ui/currency-conversion
```
Access the UI via this link: [http://127.0.0.1:8080](http://127.0.0.1:8080)
