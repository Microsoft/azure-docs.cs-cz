---
title: Nakonfigurovat mezipaměť Microsoft připojenou k aktualizaci zařízení pro Azure IoT Hub | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Přehled propojené mezipaměti Microsoftu pro aktualizaci zařízení pro Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811823"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Konfigurace připojené mezipaměti Microsoftu pro aktualizace zařízení pro Azure IoT Hub

Mezipaměť propojená Microsoftem je nasazená, aby Azure IoT Edge brány jako modul Azure IoT Edge. Stejně jako jiné moduly Azure IoT Edge, proměnné prostředí nasazení modulu MCC a možnosti vytvoření kontejneru se používají ke konfiguraci modulů mezipaměti připojené k Microsoft.  Tato část definuje proměnné prostředí a možnosti vytváření kontejnerů, které jsou nutné k tomu, aby zákazník mohl úspěšně nasadit modul Microsoft Connected cache pro použití v rámci aktualizace zařízení pro Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Podrobnosti nasazení modulu Azure IoT Edge propojené mezipaměti Microsoftu

Pojmenování modulu Microsoft Connected cache je uvážení správce. Neexistují žádné jiné interakce modulu nebo služby, které spoléhají na název modulu pro komunikaci. Kromě toho vztah nadřazeného podřízeného serveru mezipaměti od společnosti Microsoft není závislý na tomto názvu modulu, ale místo plně kvalifikovaného názvu domény nebo IP adresy Azure IoT Edge brány, která byla konfigurována jak je popsáno výše.

Proměnné prostředí Azure IoT Edge mezipaměti připojené k Microsoft jsou používány k předání základních informací o identitě modulu a nastavení funkčního modulu do kontejneru.

| Název proměnné                 | Formát hodnoty                           | Požadováno/volitelné | Funkce                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Identifikátor GUID předplatného Azure             | Vyžadováno          | Toto je klíč zákazníka, který poskytuje zabezpečení.<br>ověřování pro optimalizaci pro dodávání uzlu mezipaměti<br>Orgány.<br>Vyžaduje se, aby modul fungoval. |
| CACHE_NODE_ID                 | Identifikátor GUID ID uzlu mezipaměti                     | Vyžadováno          | Jednoznačně identifikuje mezipaměť propojenou Microsoftem.<br>Služby optimalizace pro doručení uzlu<br>Vyžadováno v pořadí<br> má modul fungovat. |
| CUSTOMER_KEY                  | GUID klíč zákazníka                     | Vyžadováno          | Toto je klíč zákazníka, který poskytuje zabezpečení.<br>ověřování uzlu mezipaměti pro doručování služeb optimalizace.<br>Vyžaduje se, aby modul fungoval.|
| STORAGE_ *N* _SIZE_GB           | Kde N je jednotka mezipaměti   | Vyžadováno          | Zadejte až 9 jednotek pro ukládání obsahu do mezipaměti a zadejte maximální místo v<br>Gigabajty k přidělení obsahu na každé jednotce mezipaměti. Příklady:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Číslo jednotky musí odpovídat zadaným hodnotám vazby jednotky mezipaměti.<br>v možnosti vytvořit kontejner MicrosoftConnectedCache *N* hodnota<br>Minimální velikost mezipaměti je 10 GB.|
| UPSTREAM_HOST                 | PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY NEBO IP ADRESA                                | Volitelné          | Tato hodnota může určovat nadřazený Microsoft.<br>Uzel mezipaměti, který funguje jako proxy, pokud je uzel připojené mezipaměti<br> je odpojený od Internetu. Toto nastavení slouží k podpoře<br> Vnořený scénář IoT.<br>**Poznámka:** Mezipaměť připojená od Microsoftu naslouchá na výchozím portu HTTP 80.|
| UPSTREAM_PROXY                | PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY/IP: PORT                           | Volitelné          | Odchozí internetový proxy server.<br>Může to být i proxy server DMZ, pokud síť ISA 95. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOSTITEL/IP<br>FQDN                        | Volitelné          | Vyžaduje se pro podporu vlastních úložišť balíčků.<br>Úložiště by se mohla hostovat místně nebo na internetu.<br>Počet vlastních hostitelů, které se dají konfigurovat, není nijak omezený.<br><br>Příklady:<br>Název = CACHEABLE_CUSTOM_1_HOST hodnota = packages.foo.com<br> Název = CACHEABLE_CUSTOM_2_HOST hodnota = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Volitelné          | Vyžaduje se pro podporu vlastních úložišť balíčků.<br>Tato hodnota se dá použít jako alias a server mezipaměti ho bude používat k odkazování.<br>různé názvy DNS. Například název hostitele obsahu úložiště může být packages.foo.com,<br>v různých oblastech ale může být další předpona, která se přidá k názvu hostitele.<br>například westuscdn.packages.foo.com a eastuscdn.packages.foo.com.<br>Nastavením kanonického aliasu zajistěte, aby se obsah neduplikoval.<br>pro obsah přicházející ze stejného hostitele, ale do různých zdrojů CDN.<br>Formát kanonické hodnoty není důležitý, ale musí být pro hostitele jedinečný.<br>Nastavení hodnoty tak, aby odpovídala hodnotě hostitele, může být nejjednodušší.<br><br>Příklady založené na příkladech vlastního hostitele:<br>Název = CACHEABLE_CUSTOM_1_CANONICAL hodnota = foopackages<br> Název = CACHEABLE_CUSTOM_2_CANONICAL hodnota = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | True nebo false                          | Volitelné          | Umožňuje zobrazení sestavy souhrnu v místní síti nebo Internetu.<br>Použití klíče rozhraní API (popsaných později) se vyžaduje k zobrazení souhrnné sestavy, pokud je nastavená na true. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| True nebo false                          | Volitelné          | Umožňuje zobrazit souhrnnou sestavu v místní síti nebo Internetu bez<br>použijte klíč rozhraní API z libovolného zařízení v síti. Použijte, pokud nechcete uzamknout přístup.<br>zobrazení souhrnných dat serveru mezipaměti prostřednictvím prohlížeče. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Možnosti vytvoření kontejneru modulu Azure IoT Edge propojených s mezipamětí Microsoftu

Možnosti vytvoření kontejneru pro nasazení modulu MCC poskytují kontrolu nad nastavením souvisejícím s úložištěm a porty používanými modulem MCC. Toto je seznam požadovaných proměnných vytvořených kontejnerů, které slouží k nasazení MCC.

### <a name="container-to-host-os-drive-mappings"></a>Mapování disků s operačním systémem na hostitele

Vyžadováno pro mapování umístění úložiště kontejneru na umístění úložiště na disku. je možné zadat < až devět umístění.

>[!Note]
>Číslo jednotky se musí shodovat s hodnotami vazby jednotky mezipaměti zadanými v proměnné prostředí STORAGE_ *N* _SIZE_GB hodnotě, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Mapování portů TCP na hostování kontejneru

Tato možnost určuje port http externího počítače, na kterém MCC naslouchá pro požadavky obsahu. Výchozí HostPort je port 80 a další porty nejsou v tuto chvíli podporovány, protože klient ADU zajišťuje žádosti na portu 80 ještě dnes. Port TCP 8081 je interní port kontejneru, na kterém naslouchá MCC, a nedá se změnit.

### <a name="container-service-tcp-port-mappings"></a>Mapování portů služby kontejnerů TCP

Modul Microsoft Connected cache obsahuje službu .NET Core, kterou modul pro ukládání do mezipaměti používá pro různé funkce.

>[!Note]
>Aby bylo možné podporovat vnořenou hraniční službu Azure IoT, nesmí být HostPort nastavené na 5000, protože modul proxy v registru už naslouchá na portu hostitele 5000.


Ukázka možností vytvoření kontejneru

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="microsoft-connected-cache-summary-report"></a>Souhrnná sestava mezipaměti propojená Microsoftem

Sestava souhrnu je aktuálně jediným způsobem, jak zákazník zobrazit data ukládání do mezipaměti pro instance služby Microsoft Connected cache nasazené do Azure IoT Edge bran. Sestava se generuje v intervalech 15 sekund a zahrnuje průměrné statistiky za období a agregované statistiky pro dobu života modulu. Klíčovým statistikám, se kterými se zákazníci budou zajímat:

* **hitBytes** – jedná se o součet dodaných bajtů, které byly získány přímo z mezipaměti.
* **missBytes** – Toto je součet bajtů dodaných do mezipaměti, aby se stáhla mezipaměť Microsoftu, aby se mohla zobrazit mezipaměť.
* **eggressBytes** – jedná se o součet hodnot HitBytes a missBytes a celkový počet bajtů dodaných klientům.
* **hitRatioBytes** – jedná se o poměr HitBytes k egressBytes.  Pokud se 100% eggressBytes dostalo v období, které se rovná hitBytes, může to být například 1.


Souhrnná sestava je k dispozici na adrese `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` nahraďte \<Azure IoT Edge Gateway IP\> IP adresou nebo názvem hostitele vaší brány IoT Edge. (informace o viditelnosti této sestavy najdete v tématu podrobnosti o proměnné prostředí).