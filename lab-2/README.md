# Laboratorium numer 2

Docker - budowa i uruchamianie pojedynczych kontenerów
Docker-compose - uruchamianie stosu

AWS EC2 - Tworzenie maszyn wirtualnych w chmurze AWS - manualnie i z wykorzystaniem AWS CloudFormation
AWS ECS - Elastic Container Service - wdrażanie aplikacji na klaster podobny do docker-service

## Przed laboratorium

Z uwagi na to, iz ćwiczenia są realizowane w trybie zdalnym proponuje dwa rozwiązania odnośnie pierwszej części ćwiczenia:

- [PlayWithDocker](https://labs.play-with-docker.com/)
- własne środowisko lokalne - System dowolny, lecz wymagana instalacja:
  - [Docker](https://docs.docker.com/engine/install/)
  - [Docker-compose](https://docs.docker.com/compose/install/) (tylko w przypadku systemów bazujących na Linuksie)

Druga część laboratorium wymaga aktywnego konta AWS i w tym celu należy wykonać instrukcje:

- wchodzimy na stronę: [AWS Free Tier](https://aws.amazon.com/free)
- zapoznajemy się z [limitami](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all) (uwaga: przekroczenie tych limitów skutkuje obciążeniem karty, która podamy podczas rejestracji!)

- zakładamy konto
- Sugestia zabezpieczeń: ustawiamy hasło z generatora (np naszego managera haseł)
- podajemy dane teleadresowe (poprawne i prawdziwe)

- podajemy kartę - polecam tutaj użycie [Revolut Virtual Card](https://help.revolut.com/help/cards/card-order/getting-a-virtual-cad/how-do-i-create-a-virtual-card) (np. z limitem np. 10$) jeśli nie posiadamy Revolut'a to podajemy kartę debetową z naszego banku
- po utworzeniu i zweryfikowaniu konta (opłata 1$) logujemy się ponownie do konsoli głównego konta `root`

[Instrukcja zerowych kosztów w AWSie](<https://aws.amazon.com/getting-started/hands-on/control-your-costs-free-tier-budgets/>
Rozpoczynamy od kroku 3
Szybkie podsumowanie kroków:

- wchodzimy do AWS Billing Dashboard
- po lewej stronie strony wyszukujemy "Budgets" i klikamy w "Create a Budget"
- tworzymy zasugerowane rozwiązanie "Zero spend budget" i w "Email recipients" podajemy nasz adres email, na który zostaniemy poinformowani, ze zbliżamy sie do limitu kosztów w ramach bezpłatnego planu

Instrukcja dodatkowych zabezpieczeń (przed wyłudzeniem klucza do konta):
<https://docs.aws.amazon.com/accounts/latest/reference/best-practices-root-user.html>

Podsumowanie dokumentu:

- [MFA](https://docs.aws.amazon.com/accounts/latest/reference/root-user-mfa.html) (dwuskładnikowe uwierzytelnienie) dla konta głównego `root` (konto z adresem e-mail)
- Konto Root'a jest kontem tylko do zarządzania, ale nie tworzenia infrastruktury!
- Osobne konto do tworzenia zasobów z możliwym logowaniem do CLI, API, Konsoli Web
[Opis jak stworzyć takiego użytkownika](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html?icmpid=docs_iam_console#id_users_create_console)
- Jakie uprawnienia nadać użytkownikowi - ja sugeruje Administratora: `AdministratorAccess policy`
Do przeczytania o tym jak to zrobić (tworzenie grupy jest opcjonalne): [Admin user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
- Po stworzeniu ukaże sie unikalny adres, na który można zalogować się bezpośrednio bez loginu głównego konta `root`, czyli tzw. adres organizacji: np. <https://123123123.signin.aws.amazon.com/console>
- zapisujemy/wysyłamy email sobie z ustawieniami wstępnymi użytkownika
- następnie logujemy sie przez stworzony dla nas adres organizacji wykorzystując stworzonego użytkownika
- zmieniamy hasło (i tu znowu sugeruje użycie generatora haseł oraz dodanie uwierzytelnienia dwuskładnikowego)

## Zadanie 1 - Docker

Uruchamianie usługi w kontekście lokalnym na skonteneryzowanym środowisku
Zadanie ma na celu przypomnienie jak należy używać Dockera

- Wykonaj fork repozytorium `iac-labs` na portalu GitHub
- Wykonaj klon repozytorium do systemu operacyjnego, w kontekście którego wykonujesz laboratorium
- Przejdź do katalogu `example-app`
- Otwórz plik `Dockerfile` i zapoznaj się z nim - jeśli coś jest w tym pliku niezrozumiałe będziemy tłumaczyć
- Wybuduj obraz z wykorzystaniem polecenia `docker build` w celu utworzenia obrazu
(do polecenia dodaj `--tag` i numerem swojego indeksu)
- Uruchom wybudowany obraz poleceniem `docker run`
- Sprawdź działanie uruchomionej aplikacji wykonując polecenie `curl http://localhost:8000` lub wchodząc pod adres z przeglądarki na swoim systemie operacyjnym
- Dodaj do powyższego polecenia dodatkowe parametry uruchomienia: `publish` - otwórz port: `8000:8000`, przekaz również plik ustawień zmiennych środowiskowych: `--env-file` nazwany `env.docker` oraz przydziel nazwę kontenerowi np `--name app`
- Uruchom dodatkowo serwer bazy danych, który nie jest opisany przez `Dockerfile` lecz z uwagi na to, iz istnieje możliwość lokalnego uruchamiania kontenerów z rejestru <https://hub.docker.com> to pobierz obraz bazy danych `postgres` (poleceniem `docker pull`)
  Następnie wykonaj polecenie `docker run --name db --env-file env.docker postgres` w celu uruchomienia
- Zweryfikuj działanie aplikacji - jeśli aplikacja nie mozę dostać sie do bazy danych to dwa poprzednie polecenia należy zmodyfikować o dodatkową siec współdzieloną `docker network create lab2zad1` i podpiąć do kontenerów które juz działają poleceniem:
  `docker network connect lab2zad1 (nazwa kontenera)`
- Zweryfikuj ponownie działanie
- Zatrzymaj kontenery - poleceniem `docker stop`
- Usuń kontenery - poleceniem `docker rm`

Pytania do zadania:

- Czym jest obraz kontenera?
- Jak działają warstwy obrazu kontenera?
- Czym różni sie kontener od obrazu?
- Dlaczego musieliśmy dodatkowo dodać siec, by komunikować dwa kontenery ze soba, skoro działają one na jednym systemie operacyjnym?
- Wymień elementy konfigurujące środowisko uruchomieniowe (_runtime environment_) w pliku `Dockerfile`

## Zadanie 2 - Docker-compose

Uruchamianie stosu aplikacji w ramach jednej maszyny

- Otwórz plik `docker-compose.yaml` i zapoznaj się z nim - jeśli coś jest w tym pliku niezrozumiałe będziemy tłumaczyć
- Uruchom stos poleceniem `docker-compose up`
- Zmodyfikuj działanie klastra dodając brakująca siec (blok `networks` i dowiązanie do kontenerów)

Pytania do zadania:

- Czym jest `docker-compose` w stosunku do poleceń Dockera z poprzedniego zadania?
- Jak nazywane są kontenery działające w ramach stosu?
- Czym jest stos aplikacji?

## Zadanie 3 - wykorzystanie EC2 jako maszyny do uruchamiania aplikacji

- Wejdź w panel AWSowy i wyszukaj usługę EC2

![EC2 Search](/assets/lab2/zad3/ec2_search.png)

- Kliknij "Launch an Instance"

![EC2 View](/assets/lab2/zad3/ec2_page.png)

- Wybierz domyślnie sugerowaną instancje bazującą na HVM
- Wybierz typ instancji T2.micro

![EC2 Create](/assets/lab2/zad3/ec2_create.png)

- Stwórz klucze do logowania przez SSH (key pair) - nazwij je jak chcesz, a następnie pobierz na swój komputer (będą potrzebne do innych zadań!)
- W ustawieniach sieciowych zauważ, ze AWS juz stworzył VPC (Virtual Private Cloud), czyli pule adresacji dla twojego rozwiązania
- Stwórz grupę zabezpieczeń (Security Group) z domyślna nazwą i otwórz dostęp SSH z twojego aktualnego adresu IP (z listy wybieralnej wybierz "My IP")

![EC2 Create Key](/assets/lab2/zad3/ec2_create_key.png)

- Naciśnij "stwórz"
- Przejdź do widoku instancji i poczekaj az stan przedzie z "Pending" do "Running"

![EC2 Run](/assets/lab2/zad3/ec2_run.png)

- Wejdź w stworzoną instancje serwera z widoku instancji i wyszukaj adres publiczny twojego serwera:

![EC2 Details](/assets/lab2/zad3/ec2_details.png)

- Teraz zaloguj sie do stworzonej instancji za pomocą polecenia `ssh -i <first_key.pem> ec2-user@<ec2-addr>`
- Jeśli udało się to: zatrzymaj maszynę

![EC2 Stop](/assets/lab2/zad3/ec2_stop.png)

- a następnie zakończ cykl maszyny wirtualnej (terminate instance) 

![EC2 Terminate](/assets/lab2/zad3/ec2_term.png)

Pytania:

- Rozwiń skrót `EC2`
- Jakiego typu wirtualizacji użyłeś do stworzenia instancji? Czy są inne możliwe typy do wykorzystania?
- Jakie narzędzie do system zarządzania pakietami dla systemów linuksowych używa stworzona przez ciebie maszyna
- Ile zasobów IaaS (AWS) wykorzystałeś w ćwiczeniu?

## Zadanie 4 - CloudFormation tworzenie pierwszego stosu

Jak w poprzednim zadaniu, lecz wykorzystując możliwości programowania infrastruktury chmury AWS
Tworzenie zasobu będziemy realizować z wykorzystaniem narzędzia CloudFormation

Notka: Wszystkie polecenia Cloud Shell można wykonywać lokalnie z wykorzystaniem
[AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
Wymaga to ustawienia zmiennych, które otrzymaliśmy w e-mailu (albo pobraliśmy) po rejestracji

- Wyszukaj serwis CloudFormation i przejdź do niego

![AWS CF](/assets/lab2/zad4/cloudformation.png)

- Na górnej belce stony po prawej stronie wyszukiwarki odszukaj ikonę konsoli

![CSH Icon](/assets/lab2/zad4/cf_cloudshell.png)

- Uruchom konsolę

![Cloud Shell](/assets/lab2/zad4/cf_csh.png)

- Sklonuj repozytorium laboratorium poleceniem `git clone` na maszynę
- Przejdź do folderu `infra/lab2/zad4`
- Zapoznaj się z przykładem tworzenia EC2 z wykorzystaniem narzędzia zdefiniowanym wewnątrz `ec2.yaml`
- Wykonaj polecenie `aws cloudformation create-stack --template-body file://ec2.yaml --stack-name first-stack --parameters ParameterKey=KeyName,ParameterValue=(nazwa klucza który stworzyłeś) ParameterKey=InstanceType,ParameterValue=t2.micro`
- Odszukaj jak w ćwiczeniu poprzednim adres maszyny (jest on również w wyniku `output` komendy tworzącej stos)
- Zaloguj sie z komputera z wykorzystaniem klucza SSH jak w poprzednim ćwiczeniu do stworzonej maszyny wirtualnej
- Usuń stos poleceniem: `aws cloudformation delete-stack --stack-name first-stack`

Pytania:

- Wewnątrz szablonu CloudFormation `ec2.yaml` znajduje się długa matryca `AWSInstanceType2Arch` wymień je ze względu na typ (pierwsze litery z nazwy)
- Sekcja `Resources` zdefiniowała tworzone zasoby wymień je
- Czym jest `AZ` w sekcji `Outputs` oraz w jakim `AvailabilityZone` jest stworzony stos?
- Czy usuwając stos o nazwie zadanej usunęliśmy wszystkie zasoby?

## Zadanie 5 - ECR - Elastic Container Registry, RDS - Relational Database Service i AppRunner

Kolejne ćwiczenie pozwoli stworzyć nasz zasób kontenerów do którego wypchniemy zbudowaną aplikacje z zadania 1

### Cześć 1: ECR

- Odszukaj ECR - Elastic Container Registry

![Search for ECR](/assets/lab2/zad5/ecr_search.png)

- Stwórz repozytorium obrazów prywatne na potrzeby ćwiczenia

![ECR Repo](/assets/lab2/zad5/ecr_repo.png)

- Przejdź do widoku repozytorium i odszukaj instrukcje wypychania obrazu

Nota: Jeśli posiadasz `aws-cli` na swoim komputerze postępuj zgodnie z instrukcją nr 1

![Push instructions](/assets/lab2/zad5/ecr_push.png)

Jeśli nie masz `aws-cli`:

- W konsoli Cloud Shell z poprzedniego ćwiczenia wykonaj polecenie: `aws ecr get-login-password`

![Cloud Shell Pwd](/assets/lab2/zad5/ecr_csh.png)

- Cały ciąg znaków skopiuj i wklej do swojego komputera do polecenia:

`docker login --username AWS --password <ciąg znaków>  <repozytorium które jest w instrukcji nr1>`

- Wybuduj obraz zgodnie z instrukcją (Notka dla ludzi z Apple MacBook M1/2 procesorami: `--platform linux/amd64` dodajemy do komendy budowania obrazu ze względu na limitacje serwisu, więc komenda wygląda musi w ten sposób
`docker build --platform linux/amd64 -t your-docker-image-name .`)
- Otaguj obraz zgodnie z instrukcją
- Wypchnij obraz zgodnie z instrukcją

![ECR Img](/assets/lab2/zad5/ecr_effect.png)

### Cześć 2: RDS - Tworzenie bazy danych do aplikacji

Po wypchnięciu kontenera aplikacji kolejnym krokiem jest stworzenie bazy danych

- Wyszukaj RDS - Relational Database Service

![RDS](/assets/lab2/zad5/rds.png)

- Naciśnij przycisk `Create Database`

![DB FreeTier](/assets/lab2/zad5/rds_free.png)

- Tworzymy bazę - uwaga mozę to zając pare minut

- Zapisujemy hasło (będzie potrzebne do zmiennych w następnym kroku)

```bash
DB_ENGINE=postgresql
DB_HOST=database-1.crlccwoii4x8.us-east-1.rds.amazonaws.com
DB_NAME=database-1
DB_USERNAME=postgres
DB_PASS=asdasdasd
DB_PORT=5432
```

### Cześć 3: AppRunner - uruchamianie kontenera z prywatnego zasobu obrazów

Ostatnim krokiem jest utworzenie instancji z obrazu z ECR oraz dołączenie połączenia do bazy danych RDS

- Wyszukujemy i wchodzimy do usługi AppRunner

![AppRunner](/assets/lab2/zad5/apprunner.png)

- Tworzymy Serwis:

![AppRunner](/assets/lab2/zad5/apprunner_serv.png)

- Konfigurujemy Serwis:

![AppRunner](/assets/lab2/zad5/apprunner_conf.png)

- Uruchamiamy i czekamy całkiem sporo czasu (ale za to za darmo)

- Gdy aplikacja nam się uruchomi weryfikujemy działanie stosu wchodząc pod sugerowany przez AppRunnera adres dostępu
- Gratulacje pierwszy deployment (manualny) aplikacji w chmurze AWS został osiągnięty
- Usuń efekty pracy, by zminimalizować szanse na otrzymanie rachunku
 (kolejność dowolna, lecz usuwamy: bazę danych z RDS, serwis z AppRunnera i repozytorium z ECR)