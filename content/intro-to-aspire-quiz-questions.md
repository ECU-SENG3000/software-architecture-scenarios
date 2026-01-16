# Aspire — Quiz Questions

Below are 20 multiple-choice questions based solely on the content in the Aspire study guide.

1. What is Aspire primarily used for?
   A. Building single-page web applications
   B. Building distributed applications with a focus on clarity, orchestration, and integration
   C. Creating mobile apps for iOS and Android
   D. Designing static websites

   **Answer:** B

2. Which programming languages does Aspire support according to the study guide?
   A. Java and Kotlin
   B. C# and Python (JavaScript coming soon)
   C. Ruby and PHP
   D. Go and Rust

   **Answer:** B

3. How are applications defined in Aspire?
   A. Through YAML configuration files only
   B. Using a code-first orchestration approach where topology is written in code
   C. By drawing diagrams in a GUI tool only
   D. With SQL schema definitions

   **Answer:** B

4. What is the role of the AppHost in an Aspire application?
   A. It stores user data and sessions
   B. It defines which services run, how they connect, and the order they start
   C. It acts as a container runtime like Docker
   D. It is a logging aggregation service

   **Answer:** B

5. What do Service Defaults provide in Aspire?
   A. Authentication and authorization rules
   B. Sensible defaults such as ports and health checks to reduce boilerplate
   C. User interface components
   D. Database migration scripts

   **Answer:** B

6. Which of the following are described as Aspire integrations?
   A. Web frameworks and CSS libraries
   B. Host integrations (Docker, Azure, Kubernetes) and client integrations (databases, messaging systems, APIs)
   C. IDE plugins and linters
   D. Image processing libraries

   **Answer:** B

7. Which Aspire CLI command runs your app locally with orchestration?
   A. `aspire build`
   B. `aspire run`
   C. `aspire test`
   D. `aspire init`

   **Answer:** B

8. What does `aspire deploy` do?
   A. Starts a local development server without orchestration
   B. Deploys your app to a target environment (Docker, Azure, etc.)
   C. Generates API documentation
   D. Creates a new Aspire project template

   **Answer:** B

9. Which CLI helper adds Docker support to a project?
   A. `aspire init docker`
   B. `aspire add docker`
   C. `aspire dockerize`
   D. `aspire create-docker`

   **Answer:** B

10. What happens when you run an Aspire CLI command in a project?
	A. It only validates syntax and exits
	B. It finds the AppHost, builds the solution, and launches dev-time orchestration
	C. It publishes to npm
	D. It compiles only the frontend assets

	**Answer:** B

11. Which builder method registers your API service and web frontend?
	A. `CreateApp`
	B. `AddProject`
	C. `RegisterService`
	D. `WithReference`

	**Answer:** B

12. Which builder method connects services (for example, frontend calling API)?
	A. `WithReference`
	B. `WaitFor`
	C. `AddDockerComposeEnvironment`
	D. `CreateBuilder`

	**Answer:** A

13. What does `WaitFor` ensure in Aspire orchestration?
	A. That logs are rotated automatically
	B. That services start in the correct order
	C. That containers use minimal memory
	D. That the build artifacts are cached

	**Answer:** B

14. What is the purpose of `WithHttpHealthCheck`?
	A. To deploy to Azure
	B. To monitor service health endpoints
	C. To add HTTPS certificates
	D. To configure load balancing

	**Answer:** B

15. What does `AddDockerComposeEnvironment` configure?
	A. A Kubernetes cluster
	B. Docker Compose for deployment
	C. A virtual machine image
	D. A serverless function

	**Answer:** B

16. What is the Aspire Dashboard used for?
	A. Writing unit tests
	B. A visual interface to monitor running services, logs, health checks, and orchestration details
	C. Managing user permissions
	D. Designing UI mockups

	**Answer:** B

17. Which of the following is NOT listed as a step in the Aspire deployment workflow?
	A. Add deployment package
	B. Update AppHost
	C. Deploy your app
	D. Write unit tests for every service

	**Answer:** D

18. Which Docker command is mentioned as an example for multi-container apps?
	A. `docker run`
	B. `docker build`
	C. `docker compose up`
	D. `docker image prune`

	**Answer:** C

19. Which tool is described as a GUI for managing containers?
	A. Kubernetes Dashboard
	B. Docker Desktop
	C. Portainer
	D. Visual Studio Code

	**Answer:** B

20. Which sequence best describes the quick example workflow referenced in the study guide for Docker?
	A. `docker compose up`, `docker ps`, `docker compose down`
	B. `docker ps`, `docker compose up`, `docker build`
	C. `docker build`, `docker push`, `docker run`
	D. `docker image ls`, `docker image rm`, `docker system prune`

	**Answer:** A

