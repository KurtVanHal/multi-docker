HOOFDSTUK 9
-----------

Dit hoofdstuk zal een applicatie bevatten die gebruik maakt van react, express, 
redis, postgres en een worker. Dit om een complexere omgeving binnen docker uit te 
bouwen.

Zie workflow.jpg voor een visualisatie van het project.

Hoofdstuk 8 omvatte de code die zal worden gebruikt.


Hoofdstuk 9 omvat het uitbouwen van de docker omgeving. In eerste instantie
wordt een Dockerfile.dev aangemaakt voor zowel server, client en worker. 
Een volgende stap is een docker-compose bestand samenstellen die containers 
aanmmaakt voor redis, postgres en de server (express). Van zodra deze kunnen 
samenwerken zullen react, nginx en de worker worden toegevoegd.

    => zie docker-compose.yml voor de configuratie.

Wanneer de services zijn ingesteld, is het belangrijk een nginx server voor de 
react en express server te plaatsen. Nginx zal, afhankelijk van het path, bepalen
welke server het request moet verwerken. Zie nginx.jpg voor workflow;

De configuratie van de nginx server bevindt zich in nginx/default.conf .

Zoals in de default.conf te zien is zal de localhost:3050 worden gebruikt om
de nginx server te bereiken. 

HOOFDSTUK 10
------------

Van zodra de docker setup volledig is, kan een multi-container setup worden voorzien
die het project zal deployen naar AWS. De workflow is te vinden in multi_container.jpg .

Een verschil met de single container deployment is dat travis de code vanuit github
zal halen, testen, en een productie image van maken. Deze wordt doorgestuurd naar
Docker hub. Travis zal dan een bericht sturen naar AWS dat de image gereed is op 
Docker hub waarna Elastic Beans de image zal 'pullen' en deployen.

1. Dockerfile's aanpassen naar Production (momenteel Development).
   Een 2de nginx server wordt aangemaakt voor de client static files te serveren.
   Zie nginx-react.jpg .

2. Github repo aanmaken + linken met Travis CI.
3. .travis.yml configureren => zie bestand

HOOFDSTUK 11
------------

Tot hiertoe werd een .travis.yml bestand samengesteld die als eindsituatie de images
naar docker hub pushte. Het is nu de bedoeling een elastic beanstalk voor deployment 
te configureren.

Om een multi container setup te deployen naar een elastic beanstalk instantie, wordt
gebruik gemaakt van een Dockerrun.aws.json configuratiebestand. Dit kan worden vergeleken
met een docker-compose.yml bestand. Met dat verschil dat de images reeds beschikbaar
zijn op docker-hub.

Elastic beanstalk maakt gebruik van de Amazon ECS service die het eigenlijke deployen
gaat doen aan de hand van de zogenaamde Task Definitions.

=> https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#container_definitions

In tegenstelling tot de development environment, zal redis en postgres niet in een 
container worden uitgevoerd. Er wordt gebruik gemaakt van aws services (RDS en Elastic cache).
Voornaamste reden hiervoor zijn
    - automatische setup
    - makkelijk schaalbaar
    - security
    - makkelijker te migreren uit EB
    - voor RDB => automatische backups en rollbacks.

Voor elke account op aws wordt per regio een vpc (virtual private cloud) gecreeërd.
Het is nodig om de EB instantie te connecteren met postgres en redis binnen dit VPC.
Om dit in orde te krijgen moet een Security Group worden aangemaakt hetgeen in 
essentie firewall rules zijn.

Connectie tss de aws services is mogelijk door een Security Group aan te maken
met daarin de 3 services en te specifiëren dat internetverkeer tss elke service
toegestaan is.

Na het in stand brengen van de security group en toevoeging aan de 3 services, kunnen 
de environment variables worden toevoegd aan de EB instantie.

Na 