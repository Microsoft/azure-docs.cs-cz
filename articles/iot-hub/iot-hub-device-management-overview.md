---
title: Správa zařízení ve službě Azure IoT Hub | Dokumentace Microsoftu
description: 'Přehled správy zařízení ve službě Azure IoT Hub: životní cyklus firemních zařízení a vzorce správy zařízení, jako je restartování, obnovení do výrobního nastavení, aktualizace firmwaru, konfigurace, dvojčata zařízení, dotazy a úlohy.'
author: bzurcher
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: 55c28b9ec39ffd617c816d76f67ff6f9853d7012
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185910"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Přehled správy zařízení ve službě IoT Hub

Azure IoT Hub poskytuje funkce a model rozšiřitelnosti, pomocí kterých mohou vývojáři zařízení a back-endů vytvářet robustní řešení pro správu zařízení IoT. Zařízení sahají od jednoduchých senzorů a jednoúčelových mikrokontrolerů po výkonné brány, které směrují komunikaci pro skupiny zařízení.  Kromě toho se způsoby použití a požadavky na operátory IoT výrazně liší v jednotlivých odvětvích.  Navzdory této variabilitě poskytuje správa zařízení pomocí IoT Hub takový rozsah možností, schémat a knihoven kódu, aby bylo možné naplnit potřeby této různorodé množiny zařízení a koncových uživatelů.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Klíčovou součástí vytvoření úspěšného podnikového řešení IoT je sestavení strategie pro průběžnou správu kolekce zařízení ze strany operátorů. Operátoři IoT vyžadují jednoduché a spolehlivé nástroje a aplikace, které jim umožní zaměřit se na strategičtější aspekty přidělených úloh. Tento článek obsahuje:

* Stručný přehled přístupu ke správě zařízení ve službě Azure IoT Hub
* Popis obecných principů správy zařízení
* Popis životního cyklu zařízení
* Přehled běžných schémat správy zařízení

## <a name="device-management-principles"></a>Principy správy zařízení
IoT s sebou nese specifickou sadu výzev v oblasti správy zařízení a každé řešení určené pro velké podniky musí odpovídat následujícím principům:

![Grafické znázornění principů správy zařízení][img-dm_principles]

* **Škálování a automatizace:** Řešení IoT vyžadují jednoduché nástroje, které mohou automatizovat běžné úlohy a umožnit poměrně málo početnému provoznímu personálu správu miliónů zařízení. V každodenním provozu operátoři očekávají, že budou moci spravovat operace se zařízeními vzdáleně a hromadně a že budou upozorňování pouze na vznik problémů, které vyžadují jejich přímou pozornost.
* **Otevřenost a kompatibilita**: Ekosystém zařízení je neobyčejně různorodý. Nástroje pro správu musí být upraveny tak, aby podporovaly velké množství tříd zařízení, platforem a protokolů. Operátoři musí být schopni poskytovat podporu pro mnoho typů zařízení: od nejjednodušších jednoprocesorových čipů po výkonné a plně funkční počítače.
* **Znalost kontextu:** Prostředí služby IoT prostředí jsou dynamická a neustále se mění. Spolehlivost služby je prvořadá. Operace správy zařízení musí vzít v úvahu následující faktory, aby se zajistilo, že výpadek kvůli údržbě neovlivní kritické obchodní operace nebo nevytvoří nebezpečné podmínky:
    * Časová období údržby SLA
    * Stavy sítě a napájení
    * Podmínky využívání
    * Zeměpisná poloha zařízení
* **Obsluha mnoha rolí**: Základním požadavkem je podpora jedinečných pracovních postupů a procesů rolí provozu služby IoT. Provozní personál musí pracovat v harmonii s danými omezeními interních oddělení IT.  Musí také najít udržitelné způsoby, jak pro vedoucí pracovníky na různých úrovních zobrazovat informace o provozu zařízení v reálném čase.

## <a name="device-lifecycle"></a>Životní cyklus zařízení
Správa zařízení probíhá v několika obecných fázích, které jsou společné pro všechny firemní projekty IoT. V Azure IoT zahrnuje životní cyklus zařízení pět fází:

![Pět fází životního cyklu zařízení Azure IoT: plánování, zřízení, konfigurace, monitorování, vyřazení][img-device_lifecycle]

V každé z těchto pěti fází existuje několik požadavků souvisejících s operátory zařízení, které by měly být splněny, aby vzniklo kompletní řešení:

* **Plánování:** Operátoři získají možnost vytvořit schéma metadat zařízení, na jehož základě mohou snadno a přesně zadávat dotazy na skupinu zařízení, kterou pak nastaví jako cíl pro hromadné operace správy. K uložení těchto metadat ve formě značek a vlastností můžete použít dvojče zařízení.
  
    *Další materiály*: [Začínáme s dvojčaty zařízení][lnk-twins-getstarted], [Principy dvojčat zařízení][lnk-twins-devguide], [postupy používat vlastnosti dvojčat zařízení][lnk-twin-properties], [osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT][lnk-adm-best-practices].
* **Zřízení:** Bezpečné zřízení nových zařízení pro IoT Hub a umožnění okamžitého zjištění možností zařízení pro operátory.  Pomocí registru identit ve službě IoT Hub můžete vytvářet flexibilní identity a přihlašovací údaje zařízení a provádět tuto operaci hromadně pomocí úlohy. Zařízení sestavujte tak, aby hlásila své možnosti a stav prostřednictvím svých vlastností v dvojčeti zařízení.
  
    *Další materiály*: [Správa identit zařízení][lnk-identity-registry], [Hromadná Správa identit zařízení][lnk-bulk-identity], [Jak používat vlastnosti dvojčat zařízení][lnk-twin-properties], [osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT][lnk-adm-best-practices], [Azure IoT Hub Device Provisioning Service][lnk-dps].
* **Konfigurace**: Provádění hromadných změn konfigurace a aktualizací firmwaru v zařízeních při zachování stavu i zabezpečení. Tyto operace správy zařízení provádějte hromadně pomocí požadovaných vlastností nebo pomocí přímých metod a vysílacích úloh.
  
    *Další materiály*: [jak používat vlastnosti dvojčat zařízení][lnk-twin-properties], [konfigurovat a monitorovat zařízení IoT ve velkém měřítku][lnk-adm-how-to], [ Osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT][lnk-adm-best-practices].
* **Monitorování:** Monitorování celkového stavu kolekce zařízení a stavu probíhajících operací, přičemž operátoři dostávají upozornění na problémy, které mohou vyžadovat jejich pozornost.  Dvojče zařízení umožní zařízením hlásit jejich provozní podmínky a stav aktualizačních operací v reálném čase. Vytvořte efektivní sestavy řídicího panelu, které budou bezprostředně informovat o problémech na základě dotazů na dvojčata zařízení.
  
    *Další materiály*: [jak používat vlastnosti dvojčat zařízení][lnk-twin-properties], [dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv] [ lnk-query-language], [Konfigurovat a monitorovat zařízení IoT ve velkém měřítku][lnk-adm-how-to], [osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT] [lnk-adm-best-practices].
* **Vyřazení:** Výměna nebo zařízení nebo jejich vyloučení z provozu po selhání, po provedení cyklu upgradů nebo na konci životnosti služby.  Pomocí dvojčete zařízení můžete provést údržbu informací o zařízení, když se nahrazuje fyzické zařízení, nebo jejich archivaci při jeho vyřazení. Pro zabezpečené odvolávání identit zařízení a přihlašovacích údajů používejte registr identit ve službě IoT Hub.
  
    *Další materiály:*[Jak používat vlastnosti dvojčat zařízení][lnk-twin-properties], [Správa identit zařízení][lnk-identity-registry].

## <a name="device-management-patterns"></a>Schémata správy zařízení
IoT Hub umožňuje využívat následující schémata správy zařízení.  V [kurzech ke správě zařízení][lnk-get-started] se podrobněji dozvíte, jak tato schémata rozšířit tak, aby vyhovovala vašemu konkrétnímu scénáři, a jak na základě těchto základních šablon navrhnout nová schémata.

* **Restartování** – Back-endová aplikace informuje zařízení prostřednictvím přímé metody, že zahájila restartování.  Zařízení využívá ohlášené vlastnosti k aktualizaci stavu restartování příslušného zařízení.
  
    ![Grafické znázornění schématu restartování ve správě zařízení][img-reboot_pattern]
* **Obnovení do výrobního nastavení** – Back-endová aplikace informuje zařízení prostřednictvím přímé metody, že zahájila obnovení do výrobního nastavení.  Zařízení využívá ohlášené vlastnosti k aktualizaci stavu obnovování výrobního nastavení příslušného zařízení.
  
    ![Grafické znázornění schématu obnovení výrobního nastavení][img-facreset_pattern]
* **Konfigurace** – Back-endová aplikace využívá požadované vlastnosti ke konfiguraci softwaru spuštěného v příslušném zařízení.  Zařízení využívá ohlášené vlastnosti k aktualizaci stavu konfigurace příslušného zařízení.
  
    ![Grafické znázornění schématu konfigurace ve správě zařízení][img-config_pattern]
* **Aktualizace firmwaru** -back endové aplikace používá konfiguraci správy automatické zařízení vyberte zařízení, která obdrží aktualizaci zařízení zjistit, kde vyhledat aktualizace a monitorování procesu aktualizace. Zařízení zahájí vícefázový proces stáhnout, ověřit a použít image firmwaru a pak proveďte restart zařízení před opětovné připojení ke službě IoT Hub.  Během tohoto vícefázového procesu zařízení využívá ohlášené vlastnosti k aktualizaci informací o průběhu zpracování a stavu zařízení.
  
    ![Grafické znázornění schématu aktualizace firmwaru ve správě zařízení][img-fwupdate_pattern]
* **Informování o průběhu a stavu** – Back-end řešení spouští dotazy dvojčete zařízení nad sadou zařízení a předává informace o stavu a průběhu zpracování akcí spuštěných v těchto zařízeních.
  
    ![Grafické znázornění schématu informování o průběhu a stavu ve správě zařízení][img-report_progress_pattern]

## <a name="next-steps"></a>Další kroky
Pomocí možností, schémat a knihoven kódu, které poskytuje správa zařízení ve službě IoT Hub, můžete vytvářet firemní aplikace IoT, které odpovídají požadavkům jejich operátorů, a to ve všech fázích jejich životního cyklu.

Pokud si chcete přečíst více o funkcích správy zařízení ve službě IoT Hub, podívejte se na kurz [Začínáme se správou zařízení][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-node-node-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-hub-getstarted]: quickstart-send-telemetry-dotnet.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-adm-best-practices]: iot-hub-configuration-best-practices.md
[lnk-adm-how-to]: iot-hub-auto-device-config.md