
# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit <https://cla.microsoft.com>.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

# Legal Notices

Microsoft and any contributors grant you a license to the Microsoft documentation and other content
in this repository under the [Creative Commons Attribution 4.0 International Public License](https://creativecommons.org/licenses/by/4.0/legalcode),
see the [LICENSE](LICENSE) file, and grant you a license to any code in the repository under the [MIT License](https://opensource.org/licenses/MIT), see the
[LICENSE-CODE](LICENSE-CODE) file.

Microsoft, Windows, Microsoft Azure and/or other Microsoft products and services referenced in the documentation
may be either trademarks or registered trademarks of Microsoft in the United States and/or other countries.
The licenses for this project do not grant you rights to use any Microsoft names, logos, or trademarks.
Microsoft's general trademark guidelines can be found at <http://go.microsoft.com/fwlink/?LinkID=254653>.

Privacy information can be found at <https://privacy.microsoft.com/en-us/>

Microsoft and any contributors reserve all other rights, whether under their respective copyrights, patents,
or trademarks, whether by implication, estoppel or otherwise.

# Setup

## [Test the subscription key](https://docs.microsoft.com/en-us/learn/modules/control-authentication-with-apim/3-exercise-create-subscriptions-in-apim)

```bash
# /api/Weather/{latitude}/{longitude
curl -X GET $API_GW_URL/api/Weather/53/-1

curl -X GET $API_GW_URL/api/Weather/53/-1 \
  -H "Ocp-Apim-Subscription-Key: ${APIM_SUB_KEY}"
```

## [Use client certificates to secure access to an API](https://docs.microsoft.com/en-us/learn/modules/control-authentication-with-apim/5-exercise-secure-access-client-certs)

```bash
export MSYS_NO_PATHCONV=1

# Create self-signed certificate
pwd='Password123!'
mkdir -p cert/
pKey='cert/privateKey.key'
pfxFilePath='cert/selfsigncert.pfx'
certOutFilePath='cert/selfsigncert.crt'
pemOutFilePath='cert/selfsigncert.pem'

openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout $pKey -out $certOutFilePath -subj /CN=localhost

openssl pkcs12 -export -out $pfxFilePath -inkey $pKey -in $certOutFilePath -password pass:$pwd
openssl pkcs12 -in $pfxFilePath -out $pemOutFilePath -nodes

# Certificate thumbprint

Fingerprint="$(openssl x509 -in $pemOutFilePath -noout -fingerprint)"
Fingerprint="${Fingerprint//:}"
echo ${Fingerprint#*=}


# Test the API with the self-signed cert
curl -X GET $API_GW_URL/api/Weather/53/-1 \
  -H "Ocp-Apim-Subscription-Key: ${APIM_SUB_KEY}" \
  --cert-type pem \
  --cert $pemOutFilePath
```
