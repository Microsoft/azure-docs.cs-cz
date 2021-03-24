---
title: Řešení obecných problémů s Azure Percept DK a IoT Edge
description: Získejte tipy pro řešení problémů s některými častými problémy s Azure Percept DK.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 313ea98da0426af945dfdea00d33440ab2955cc7
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023074"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Řešení potíží s Azure Percept DK (dev Kit)

Obecné tipy pro odstraňování potíží s Azure Percept DK najdete v následujících pokynech.

## <a name="general-troubleshooting-commands"></a>Obecné příkazy pro řešení potíží

Chcete-li spustit tyto příkazy, 
1. Připojení k [Wi-Fimu bodu sady pro vývojáře](./quickstart-percept-dk-set-up.md)
1. [SSH do sady dev Kit](./how-to-ssh-into-percept-dk.md)
1. Zadejte příkazy v terminálu SSH.

K přesměrování libovolného výstupu do souboru. txt k další analýze použijte následující syntaxi:

```console
sudo [command] > [file name].txt
```

Změňte oprávnění souboru. txt tak, aby bylo možné ho zkopírovat:

```console
sudo chmod 666 [file name].txt
```

Po přesměrování výstupu do souboru. txt zkopírujte soubor do hostitelského počítače přes bod připojení služby (SCP):

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` odkazuje na umístění na hostitelském počítači, do kterého chcete zkopírovat soubor. txt. ```[remote username]``` je uživatelské jméno SSH zvolené během [prostředí pro nastavení](./quickstart-percept-dk-set-up.md). Pokud jste během počátečního nastavení OOBE nevytvořili přihlašovací jméno SSH, je vzdálené uživatelské jméno ```root``` .

Další informace o příkazech Azure IoT Edge naleznete v dokumentaci k [řešení potíží se zařízením Azure IoT Edge](../iot-edge/troubleshoot.md).

|Kategorie:         |Systému                    |Slouží                  |
|------------------|----------------------------|---------------------------|
|Operační systém                |```cat /etc/os-release```         |ověřit verzi image Mariner |
|Operační systém                |```cat /etc/os-subrelease```      |zkontroluje verzi odvozené image. |
|Operační systém                |```cat /etc/adu-version```        |ověřit verzi ADU |
|Teplota       |```cat /sys/class/thermal/thermal_zone0/temp``` |kontrolovat teplotu DevKit |
|Wi-Fi             |```sudo journalctl -u hostapd.service``` |kontrolovat protokoly SoftAP|
|Wi-Fi             |```sudo journalctl -u wpa_supplicant.service``` |kontrolovat protokoly služby Wi-Fi Services |
|Wi-Fi             |```sudo journalctl -u ztpd.service```  |kontrolovat Wi-Fi nenulové protokoly služby Touch Provisioning |
|Wi-Fi             |```sudo journalctl -u systemd-networkd``` |ověřit protokoly síťového zásobníku Mariner |
|Wi-Fi             |```sudo cat /etc/hostapd/hostapd-wlan1.conf``` |Ověřte podrobnosti konfigurace přístupového bodu sítě Wi-Fi. |
|OBSAH              |```sudo journalctl -u oobe -b```       |kontrolovat protokoly OOBE |
|Telemetrie         |```sudo azure-device-health-id```      |najít jedinečnou HW_ID telemetrie |
|Azure IoT Edge          |```sudo iotedge check```          |Spouštění kontrol konfigurace a připojení pro běžné problémy |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |kontrolovat protokoly kontejnerů, jako jsou řeč a moduly Vision |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |shromažďovat protokoly modulů, Azure IoT Edge protokoly správce zabezpečení, protokoly kontejnerů, ```iotedge check``` výstup JSON a další užitečné ladicí informace za poslední hodinu |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |zobrazení protokolů Azure IoT Edge Security Manager |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |Restartujte proces démona zabezpečení Azure IoT Edge. |
|Azure IoT Edge          |```sudo iotedge list```           |Vypsat nasazené Azure IoT Edge moduly |
|Jiné             |```df [option] [file]```          |Zobrazit informace o dostupném/celkovém prostoru v zadaném systému souborů |
|Jiné             |`ip route get 1.1.1.1`        |Zobrazit informace o IP a rozhraních zařízení |
|Jiné             |<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |Zobrazit jenom IP adresu zařízení |


```journalctl```Příkazy Wi-Fi lze kombinovat do následujícího jednoho příkazu:

```console
sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Příkazy pro řešení potíží Docker

|Systému                        |Slouží                  |
|--------------------------------|---------------------------|
|```sudo docker ps``` |[zobrazuje spuštěné kontejnery.](https://docs.docker.com/engine/reference/commandline/ps/) |
|```sudo docker images``` |[zobrazuje, které image jsou na zařízení.](https://docs.docker.com/engine/reference/commandline/images/)|
|```sudo docker rmi [image id] -f``` |[odstraní obrázek ze zařízení.](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```sudo docker logs -f edgeAgent``` <br> ```sudo docker logs -f [module_name]``` |[převezme protokoly kontejneru zadaného modulu.](https://docs.docker.com/engine/reference/commandline/logs/) |
|```sudo docker image prune``` |[Odebere všechny image dangling.](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```sudo watch docker ps``` <br> ```watch ifconfig [interface]``` |Podívejte se na stav stahování kontejneru Docker. |

## <a name="usb-updating"></a>Aktualizace USB

|Chyba:                                    |Řešení:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX během USB Flash přes UUU |Tato chyba je výsledkem chyby připojení USB během UUU aktualizace. Pokud kabel USB není správně připojený k portům USB na počítači nebo v PE-10X, dojde k chybě tohoto formuláře. Zkuste odpojte a znovu dopojte oba konce kabelu USB a jiggling kabel, aby se zajistilo zabezpečené připojení. Téměř vždy problém vyřeší. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Stavy LED nosné desky pro Azure Percept DK

Existují tři malé indikátory LED na nosných deskách. Ikona cloudu se tiskne vedle indikátoru LED 1, ikona Wi-Fi se vytiskne vedle indikátoru LED 2 a ikona vykřičníku se zobrazí vedle indikátoru LED 3. Informace o jednotlivých stavech LED najdete v následující tabulce.

|POD             |State      |Popis                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT Hub) |Zapnuto (Solid) |Zařízení je připojené k IoT Hub. |
|Indikátor LED 2 (Wi-Fi)   |Pomalé blikání |Zařízení je připravené k nakonfigurování Wi-Fi snadné připojení a oznamuje jeho přítomnost do konfigurátoru. |
|Indikátor LED 2 (Wi-Fi)   |Rychlé blikání |Ověřování bylo úspěšné, probíhá přidružení zařízení. |
|Indikátor LED 2 (Wi-Fi)   |Zapnuto (Solid) |Ověřování a přidružení byly úspěšné. zařízení je připojené k síti Wi-Fi. |
|INDIKÁTOR LED 3           |NA         |Indikátor LED se nepoužívá. |