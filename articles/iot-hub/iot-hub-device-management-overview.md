---
title: Přehled správy zařízení pomocí Azure IoT Hub | Microsoft Docs
description: Přehled správy zařízení v Azure IoT hu – životní cyklus zařízení a vzory správy zařízení, jako je restartování, obnovení továrního nastavení, aktualizace firmwaru, konfigurace, vlákna zařízení, dotazy a úlohy.
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 9ff8ebeaf7c4a219bc403c81dfea62f4657a1fd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148347"
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

![Grafické znázornění principů správy zařízení](media/iot-hub-device-management-overview/image4.png)

* **Škálování a automatizace:** Řešení IoT vyžadují jednoduché nástroje, které mohou automatizovat běžné úlohy a umožnit poměrně málo početnému provoznímu personálu správu miliónů zařízení. V každodenním provozu operátoři očekávají, že budou moci spravovat operace se zařízeními vzdáleně a hromadně a že budou upozorňování pouze na vznik problémů, které vyžadují jejich přímou pozornost.

* **Otevřenost a kompatibilita**: Ekosystém zařízení je neobyčejně různorodý. Nástroje pro správu musí být upraveny tak, aby podporovaly velké množství tříd zařízení, platforem a protokolů. Operátoři musí být schopni poskytovat podporu pro mnoho typů zařízení: od nejjednodušších jednoprocesorových čipů po výkonné a plně funkční počítače.

* **Znalost kontextu:** Prostředí služby IoT prostředí jsou dynamická a neustále se mění. Spolehlivost služby je prvořadá. Operace správy zařízení musí vzít v úvahu následující faktory, aby se zajistilo, že výpadek kvůli údržbě neovlivní kritické obchodní operace nebo nevytvoří nebezpečné podmínky:

    * Časová období údržby SLA
    * Stavy sítě a napájení
    * Podmínky využívání
    * Zeměpisná poloha zařízení

* **Obsluha mnoha rolí:** Základním požadavkem je podpora jedinečných pracovních postupů a procesů rolí provozu služby IoT. Provozní personál musí pracovat v harmonii s danými omezeními interních oddělení IT.  Musí také najít udržitelné způsoby, jak pro vedoucí pracovníky na různých úrovních zobrazovat informace o provozu zařízení v reálném čase.

## <a name="device-lifecycle"></a>Životní cyklus zařízení
Správa zařízení probíhá v několika obecných fázích, které jsou společné pro všechny firemní projekty IoT. V Azure IoT zahrnuje životní cyklus zařízení pět fází:

![Pět fází životního cyklu zařízení Azure IoT: plánování, zřízení, konfigurace, monitorování, vyřazení](./media/iot-hub-device-management-overview/image5.png)

V každé z těchto pěti fází existuje několik požadavků souvisejících s operátory zařízení, které by měly být splněny, aby vzniklo kompletní řešení:

* **Plánování:** Operátoři získají možnost vytvořit schéma metadat zařízení, na jehož základě mohou snadno a přesně zadávat dotazy na skupinu zařízení, kterou pak nastaví jako cíl pro hromadné operace správy. K uložení těchto metadat ve formě značek a vlastností můžete použít dvojče zařízení.
  
    *Další čtení*: 
    * [Začínáme s dvojčaty zařízení](iot-hub-node-node-twin-getstarted.md)
    * [Principy dvojčat zařízení](iot-hub-devguide-device-twins.md)
    * [Jak používat zdvojené vlastnosti zařízení](tutorial-device-twins.md)
    * [Osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT](iot-hub-configuration-best-practices.md)

* **Zřízení:** Bezpečné zřízení nových zařízení pro IoT Hub a umožnění okamžitého zjištění možností zařízení pro operátory.  Pomocí registru identit ve službě IoT Hub můžete vytvářet flexibilní identity a přihlašovací údaje zařízení a provádět tuto operaci hromadně pomocí úlohy. Zařízení sestavujte tak, aby hlásila své možnosti a stav prostřednictvím svých vlastností v dvojčeti zařízení.
  
    *Další čtení*: 
    * [Správa identit zařízení](iot-hub-devguide-identity-registry.md)
    * [Hromadná Správa identit zařízení](iot-hub-bulk-identity-mgmt.md)
    * [Jak používat zdvojené vlastnosti zařízení](tutorial-device-twins.md)
    * [Osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT](iot-hub-configuration-best-practices.md)
    * [Služba Azure IoT Hub Device Provisioning](https://azure.microsoft.com/documentation/services/iot-dps)

* **Konfigurace:** Provádění hromadných změn konfigurace a aktualizací firmwaru v zařízeních při zachování stavu i zabezpečení. Tyto operace správy zařízení provádějte hromadně pomocí požadovaných vlastností nebo pomocí přímých metod a vysílacích úloh.
  
    *Další čtení*:
    * [Jak používat zdvojené vlastnosti zařízení](tutorial-device-twins.md)
    * [Konfigurace a monitorování škálování zařízení IoT](./iot-hub-automatic-device-management.md)
    * [Osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT](iot-hub-configuration-best-practices.md)

* **Monitorování:** Monitorování celkového stavu kolekce zařízení a stavu probíhajících operací, přičemž operátoři dostávají upozornění na problémy, které mohou vyžadovat jejich pozornost.  Dvojče zařízení umožní zařízením hlásit jejich provozní podmínky a stav aktualizačních operací v reálném čase. Vytvořte efektivní sestavy řídicího panelu, které budou bezprostředně informovat o problémech na základě dotazů na dvojčata zařízení.
  
    *Další čtení*: 
    * [Jak používat zdvojené vlastnosti zařízení](tutorial-device-twins.md)
    * [IoT Hub dotazovací jazyk pro vlákna, úlohy a směrování zpráv v zařízeních](iot-hub-devguide-query-language.md)
    * [Konfigurace a monitorování škálování zařízení IoT](./iot-hub-automatic-device-management.md)
    * [Osvědčené postupy pro konfiguraci zařízení v rámci řešení IoT](iot-hub-configuration-best-practices.md)

* **Vyřazení**: výměna nebo vyřazení zařízení z provozu po selhání, cyklus upgradu nebo na konci životnosti služby.  Pomocí dvojčete zařízení můžete provést údržbu informací o zařízení, když se nahrazuje fyzické zařízení, nebo jejich archivaci při jeho vyřazení. Pro zabezpečené odvolávání identit zařízení a přihlašovacích údajů používejte registr identit ve službě IoT Hub.
  
    *Další čtení*: 
    * [Jak používat zdvojené vlastnosti zařízení](tutorial-device-twins.md)
    * [Správa identit zařízení](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Schémata správy zařízení

IoT Hub umožňuje využívat následující schémata správy zařízení. V [kurzech ke správě zařízení](iot-hub-node-node-device-management-get-started.md) se podrobněji dozvíte, jak tato schémata rozšířit tak, aby vyhovovala vašemu konkrétnímu scénáři, a jak na základě těchto základních šablon navrhnout nová schémata.

* **Restart**: aplikace back-end informuje zařízení prostřednictvím přímé metody, že zahájil restart.  Zařízení využívá ohlášené vlastnosti k aktualizaci stavu restartování příslušného zařízení.
  
    ![Grafické znázornění schématu restartování ve správě zařízení](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Obnovení továrního nastavení**: aplikace back-end informuje zařízení prostřednictvím přímé metody, že zahájilo obnovení továrního nastavení. Zařízení využívá ohlášené vlastnosti k aktualizaci stavu obnovování výrobního nastavení příslušného zařízení.
  
    ![Grafické znázornění schématu obnovení výrobního nastavení](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Konfigurace**: aplikace back-end používá požadované vlastnosti ke konfiguraci softwaru spuštěného v zařízení. Zařízení využívá ohlášené vlastnosti k aktualizaci stavu konfigurace příslušného zařízení.
  
    ![Grafické znázornění schématu konfigurace ve správě zařízení](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Aktualizace firmwaru**: back-end aplikace používá automatickou konfiguraci správy zařízení k výběru zařízení, která mají být aktualizace k dismailu, k oznámení zařízení, ve kterých se má aktualizace Hledat, a k monitorování procesu aktualizace. Zařízení zahájí proces s více kroky ke stažení, ověření a instalaci image firmwaru a následné restartování zařízení před opětovným připojením ke službě IoT Hub. Během tohoto vícefázového procesu zařízení využívá ohlášené vlastnosti k aktualizaci informací o průběhu zpracování a stavu zařízení.
  
    ![Grafické znázornění schématu aktualizace firmwaru ve správě zařízení](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Vytváření sestav o průběhu a stavu**: back-end řešení spouští v rámci sady zařízení zdvojené dotazy na zařízení, aby hlásil stav a průběh akcí spuštěných v zařízeních.
  
    ![Grafické znázornění schématu informování o průběhu a stavu ve správě zařízení](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Další kroky

Pomocí možností, schémat a knihoven kódu, které poskytuje správa zařízení ve službě IoT Hub, můžete vytvářet firemní aplikace IoT, které odpovídají požadavkům jejich operátorů, a to ve všech fázích jejich životního cyklu.

Pokud si chcete přečíst více o funkcích správy zařízení ve službě IoT Hub, podívejte se na kurz [Začínáme se správou zařízení](iot-hub-node-node-device-management-get-started.md).