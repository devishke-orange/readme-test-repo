# ReadMe Push Process

## Dependencies
- [jq](https://jqlang.github.io/jq/)
- [rdme](https://github.com/readmeio/rdme)

## Extracting Tags
`cat orangehrm-v2.json | jq .paths[][]?.tags[] | uniq | sort > tag-list.txt`

- Get text in `tags` object 
- Get unique list
- Sort alphabetically
- Output to text file

## Pushing per Module

E.g. for Admin module

1. `cat tag-list.txt | grep Admin/ | sed 's/"/--tag "/' | tr '\n' ' ' | sed '1s/^/rdme openapi:reduce orangehrm-v2.json --out reduced.json /' > reduce-command.sh`

- Find all Admin tags in `tag-list.txt`
- Prepend `--tag ` to start of all entries
- Remove line breaks
- Prepend `rdme openapi:reduce` command to the start
- Output to `sh` file

2. `chmod +x reduce-command.sh`

- Make `sh` file executable

3. `./reduce-command.sh`

- Run command
- This will create a reduced API specification including only the tags that were specified

4. `sed -i 's/Admin\///' reduced.json`

- Remove `Admin/` part from all Tags

5. `sed -i 's/OrangeHRM Open Source : REST API v2 docs/Admin/' reduced.json`

- Change title of OpenAPI spec to Admin

6. `jq '.components+={"securitySchemes": {"OAuth2": {"type":"oauth2", "flows": {"authorizationCode":{"authorizationUrl":"https://opensource-demo.orangehrmlive.com/web/index.php/oauth2/authorize", "tokenUrl": "https://opensource-demo.orangehrmlive.com/web/index.php/oauth2/token", "scopes":{}}}}}}' reduced.json > reduced-security.json`

- Add `securitySchemes` object within `components`
- Seems `rdme openapi:reduce` does not include the `securitySchemes`
- Have to add it in manually
- Output to `reduced-security.json`

7. `rdme openapi reduced-security.json --key=${{ secrets.README_API_KEY }} --id=64d991ddaf28d60045b5184c`

- Update existing specification using `rdme`

8. `rm reduce*`

- Remove `reduce-command.sh`, `reduced.json`, `reduced-security.json`
- If this is not done it will cause errors for the next module since `rdme` does not overwrite existing files

## Repeat

Repeat the process for other modules