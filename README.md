# dev-ferris Home Assistant Add-ons

Dieses Repository ist eine Sammlung von Add-ons für [Home Assistant](https://www.home-assistant.io/)
und kann direkt als zusätzliche Add-on-Quelle (Repository) im Home Assistant
Add-on Store eingebunden werden.

## Repository in Home Assistant einbinden

Damit die Add-ons aus diesem Repository im Add-on Store erscheinen, muss die
Repository-URL einmalig in Home Assistant hinterlegt werden.

### Variante 1: Per Klick (My Home Assistant Link)

Wer einen My-Home-Assistant-Link nutzen möchte, kann diesen Link öffnen:

<https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fdev-ferris%2Fhassio-addons>

Home Assistant fragt anschließend, ob das Repository hinzugefügt werden soll –
einfach bestätigen.

### Variante 2: Manuell über die Oberfläche

1. In Home Assistant **Einstellungen → Add-ons → Add-on Store** öffnen.
2. Oben rechts auf das Drei-Punkte-Menü (**⋮**) klicken und **Repositorys**
   auswählen.
3. Folgende URL einfügen und auf **Hinzufügen** klicken:

   ```
   https://github.com/dev-ferris/hassio-addons
   ```

4. Den Dialog schließen. Im Add-on Store erscheint nun ein neuer Abschnitt
   **dev-ferris Home Assistant Add-ons** mit allen verfügbaren Add-ons aus
   diesem Repository.

> **Hinweis:** Das Einbinden eines Repositorys setzt voraus, dass der Home
> Assistant **Supervisor** läuft (z. B. Home Assistant OS oder Home Assistant
> Supervised). Auf reinen Home-Assistant-Container- oder Core-Installationen
> steht der Add-on Store nicht zur Verfügung.

## Add-on installieren

Nachdem das Repository eingebunden ist, lassen sich die einzelnen Add-ons wie
gewohnt installieren:

1. **Einstellungen → Add-ons → Add-on Store** öffnen.
2. Zum Abschnitt **dev-ferris Home Assistant Add-ons** scrollen.
3. Das gewünschte Add-on auswählen und auf **Installieren** klicken.
4. Auf dem Reiter **Konfiguration** die Optionen anpassen
   (siehe README des jeweiligen Add-ons).
5. Auf dem Reiter **Info** das Add-on starten und nach Bedarf
   *Beim Booten starten* sowie *Watchdog* aktivieren.

## Verfügbare Add-ons

| Add-on | Beschreibung | Dokumentation |
| --- | --- | --- |
| **Hawser** | Schlanker Go-Agent, mit dem [Dockhand](https://github.com/Finsys/dockhand) Docker-Hosts hinter Firewalls, NATs oder in Cluster-Setups verwalten kann. | [hawser/README.md](hawser/README.md) |

Die Konfigurationsoptionen und alle weiteren Details sind in der README des
jeweiligen Add-ons beschrieben.

## Updates

Aktualisierungen werden automatisch im Add-on Store angezeigt, sobald eine neue
Version dieses Repositorys veröffentlicht wird. Über **Einstellungen → Add-ons**
kann das Update mit einem Klick eingespielt werden.

## Support

- Probleme mit den Add-ons aus diesem Repository bitte als Issue unter
  <https://github.com/dev-ferris/hassio-addons/issues> melden.
- Probleme mit der jeweils zugrunde liegenden Software (z. B. Hawser selbst)
  gehören in das jeweilige Upstream-Projekt.

## Lizenz

Dieses Repository steht unter der [MIT-Lizenz](LICENSE).

## Danksagung

Dieses Repository wurde mit Unterstützung von [Claude](https://claude.com/claude-code),
dem KI-Assistenten von Anthropic, erstellt.
