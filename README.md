## Dokumentacja CI/CD

W tym projekcie wdrożono zautomatyzowane procesy Continuous Integration oraz Continuous Deployment.

### 1. Dostępne Workflowy
W projekcie skonfigurowano dwa główne przepływy pracy:

*   **CI Workflow (`ci.yml`)**: 
    Uruchamia się automatycznie przy każdym zdarzeniu `push` oraz `pull_request` skierowanym do gałęzi `main`. Odpowiada za:
    *   Instalację środowiska i linterów.
    *   Równoległe sprawdzanie formatowania (Flake8, Black, isort) oraz bezpieczeństwa (Bandit, Safety).
    *   Uruchomienie testów jednostkowych i integracyjnych Django z wykorzystaniem baz PostgreSQL oraz Redis działających w kontenerach.
*   **Deploy Workflow (`deploy.yml`)**: 
    Uruchamia się wyłącznie i automatycznie po udanym złączeniu (merge) kodu z gałęzią `main`. Buduje nowy obraz Docker, loguje się do rejestru (Docker Registry), wysyła obraz, a następnie łączy się przez SSH z serwerem w celu wdrożenia nowej wersji aplikacji.

### 2. Wymagane Sekrety
Aby pipeline działał poprawnie bez błędów, w ustawieniach repozytorium GitHub (**Settings -> Secrets and variables -> Actions**) muszą zostać zdefiniowane następujące zmienne (Repository secrets):

**Dla środowiska testowego (CI):**
*   `POSTGRES_DB` - nazwa testowej bazy danych
*   `POSTGRES_USER` - użytkownik bazy danych
*   `POSTGRES_PASSWORD` - hasło użytkownika

**Dla środowiska produkcyjnego (Deploy):**
*   `DOCKER_USERNAME` - login do rejestru obrazów (np. DockerHub)
*   `DOCKER_PASSWORD` - hasło/token do rejestru obrazów
*   `SERVER_HOST` - adres IP serwera docelowego
*   `SERVER_USER` - nazwa użytkownika SSH na serwerze
*   `SERVER_SSH_KEY` - klucz prywatny SSH do autoryzacji

### 3. Debugowanie błędów
Jeśli workflow zakończy się statusem "Failed" (czerwony krzyżyk), wejdź w zakładkę **Actions** na GitHubie, wybierz nieudany przebieg (Run) i kliknij w zadanie, które zgłosiło błąd. Logi wskażą konkretny krok i przyczynę błędu (np. niespełnione zasady lintera, brakujące sekrety lub oblewające testy).
