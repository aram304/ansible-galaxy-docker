# Ansible Role: Docker

Deze Ansible-role installeert Docker op Ubuntu-hosts en start een eenvoudige Hello World-container. De role wordt gebruikt binnen de les-06 CI/CD-opdracht om automatisch een Docker-container uit te rollen op zowel een Azure VM als een ESXi VM.

## Doel

Het doel van deze role is om Docker automatisch te installeren, een vooraf gebouwde Docker-image te laden en daarna een Hello World-container te starten op poort `8080`.

De role voert ook een controle uit om te bevestigen dat de container bereikbaar is.

## Requirements

Deze role is bedoeld voor Ubuntu-hosts.

Benodigdheden:

* Ansible
* Ubuntu host
* SSH-toegang tot de doelhost
* `become: true`
* Ansible facts, omdat `ansible_distribution_release` wordt gebruikt
* Collection `community.docker`

Installeer de benodigde collection met:

```bash
ansible-galaxy collection install community.docker
```

## Role Variables

De volgende variabele staat standaard in `defaults/main.yml`:

```yaml
docker_users:
  - testuser
```

Deze variabele kan gebruikt worden om aan te geven welke gebruiker met Docker moet werken. In de huidige versie wordt Docker vooral systeemwijd geïnstalleerd en wordt de container via Ansible gestart.

## Wat de role doet

De role voert de volgende stappen uit:

1. Installeert benodigde pakketten zoals `ca-certificates`, `curl`, `python3` en `python3-requests`.
2. Maakt de Docker keyring-map aan.
3. Downloadt de officiële Docker GPG-sleutel.
4. Voegt de officiële Docker repository toe.
5. Installeert Docker Engine en bijbehorende componenten.
6. Start de Docker-service en zet automatisch starten aan.
7. Controleert of Docker correct is geïnstalleerd.
8. Kopieert `hello-world.tar` naar de host.
9. Laadt de Docker-image.
10. Start de Hello World-container.
11. Controleert of de container bereikbaar is via `http://127.0.0.1:8080`.

## Verwachte Docker-image

De role verwacht dat er al een Docker-image als tar-bestand bestaat op deze locatie:

```text
{{ playbook_dir }}/hello-world.tar
```

In de les-06 workflow wordt deze image vooraf gebouwd en opgeslagen met:

```bash
docker save hello-world:latest -o ansible/hello-world.tar
```

Daarna kan Ansible deze image naar de VM kopiëren en daar laden.

## Example Playbook

Voorbeeld van het gebruik van deze role:

```yaml
- name: Docker installeren en Hello World-container starten
  hosts: azure:esxi
  become: true
  gather_facts: true

  roles:
    - Docker
```

Wanneer de role via Ansible Galaxy is geïnstalleerd, kan de role bijvoorbeeld zo worden aangeroepen:

```yaml
- name: Docker installeren en Hello World-container starten
  hosts: azure:esxi
  become: true
  gather_facts: true

  roles:
    - aramairapetian.docker
```

## Installatie via Ansible Galaxy

Na publicatie op Ansible Galaxy kan de role worden geïnstalleerd met:

```bash
ansible-galaxy role install aramairapetian.docker
```

De informatie van de gepubliceerde role kan gecontroleerd worden met:

```bash
ansible-galaxy role info aramairapetian.docker
```

## Gebruik in requirements.yaml

De role kan reproduceerbaar worden geïnstalleerd via `requirements.yaml`:

```yaml
---
roles:
  - name: aramairapetian.docker

collections:
  - name: community.docker
  - name: ansible.posix
```

Installeren kan daarna met:

```bash
ansible-galaxy install -r requirements.yaml
```

## Controle

Na uitvoering controleert de role of:

* Docker correct is geïnstalleerd;
* de Docker-service actief is;
* de Hello World-image geladen is;
* de container gestart is;
* de webpagina bereikbaar is op poort `8080`.

## License

MIT

## Author

Aram Airapetian
