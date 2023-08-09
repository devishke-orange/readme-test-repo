# readme-test-repo

Commands
cat orangehrm-v2.json | jq .paths[][]?.tags[] | sed 's/"/--tag "/' | grep Admin/ | uniq |tr '\n' ' ' | sed '1s/^/rdme openapi:reduce --out admin.json /' > admin-command.sh

chmod +x admin-command.sh

./admin-command.sh