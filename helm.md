# Helm
"The Package Manager for Kubernetes"

Learn Helm (YouTube) - https://www.youtube.com/watch?v=OSmQj9ZQv8g&list=PLSwo-wAGP1b8svO5fbAr7ko2Buz6GuH1g&index=4

## Adding a Helm Repository
helm repo add <name_you_give_for_repo> <repo_url>
helm repo update  # good practice to update Helm repo

## Installing a chart
helm install <release_name_you_decide> <repo_name>/<chart_name>

