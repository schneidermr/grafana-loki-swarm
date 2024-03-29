# grafana-loki-swarm

Grafana Loki minimal setup for Docker Swarm.

It is not recommended to use this exact setup for production.

It is supposed to a _starting point_ for people who want to run Grafana and Loki in a Docker Swarm stack.

You will need to adapt it for your requirements.

# Steps to Use Docker Compose File with Docker Swarm:

1. Initialize Docker Swarm

   Before deploying the stack to Docker Swarm, ensure that Docker Swarm is initialized. If you are using a single node (your local machine), you can initialize it using:
   ```bash
   docker swarm init
   ```
   If you have multiple nodes, Docker will provide you with a docker swarm join command to run on worker nodes.
2. Config files:

   Ensure that the configuration files specified under configs is available in Docker. As they are marked as external, you need to create them in Docker before deploying the stack:

   ```bash
   docker config create loki_config ./loki-config.yaml
   docker config create promtail_config ./promtail-config.yaml
   ```
   
3. Deploying the Stack

   Deploy the stack using docker stack deploy. 

   ```bash
   docker stack deploy -c grafana-loki-stack.yml my-stack
   ```

   Replace `my-stack` with the name you'd like to give to your stack.

4. Viewing test logs
   
   For testing purposes, flog creates log entries every second. To view them in Grafana, follow these steps:
   1. Find out the flog container name: 
      ```bash
      docker inspect $(docker ps -q -f name=<my_stack>_flog) | jq -r '.[0].Name' | sed 's/^\///'`
      ```
      Replace `<my-stack>` with the actual stack name you have chosen.
   2. In Grafana, go to "Connections > Datasources > Loki > Explore" and click on "code".
      Enter this as query code:
      ```
      {container="<flog_container_name>"} 
      ```
      Replace `<my-flog_container_name>` with the actual container name.

5. Accessing Grafana

   Once deployed, you should be able to access Grafana on http://localhost:3000 (or using your Docker node’s IP address).

6. Removing the Stack

   When you want to remove the stack:

   ```bash
   docker stack rm my-stack
   ```

Notes:

- Ensure Docker is running in swarm mode and your node is active.
- Ensure that all path references and external configurations are available and accurate.
- Always validate the configuration file using docker-compose config to avoid runtime issues.
- Monitor logs for loki and grafana services to troubleshoot any issues during runtime.

With these steps, your services defined in the Docker Compose file should be deployed and managed by Docker Swarm.