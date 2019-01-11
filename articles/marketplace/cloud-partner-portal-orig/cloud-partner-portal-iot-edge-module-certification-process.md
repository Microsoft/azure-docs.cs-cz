---
title: Certifikace modul IoT Edge | Dokumentace Microsoftu
description: Certifikujte modul IoT Edge pro web Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c8056bd4912605a4cd3ee333b1be87d4f3a6d5ba
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198044"
---
# <a name="the-iot-edge-module-certification-process"></a>Proces certifikace modul IoT Edge

Tento článek popisuje kroky a požadavky na certifikaci modul IoT Edge pro publikování na webu Azure Marketplace. 

>[!Note]
>Toto je dočasný dokumentu. Marketplace modul IoT Edge sestavuje paralelně a v tomto článku se nahradí veřejné dokumentaci.

## <a name="understanding-an-iot-edge-module"></a>Principy modul IoT Edge

Terminologie modul:

-   A **image s modulem** je balíček, který obsahuje software, který definuje modulu.

-   A **instancí modulu** je konkrétní jednotka výpočtu spuštění bitové kopie modulu na zařízení IoT Edge. Instance modulu je spuštěn modul runtime IoT Edge.

Moduly mohou zahrnovat také modulu IoT SDK, která používá následující terminologií:

-   A **modul identity** je část informací (včetně zabezpečovací přihlašovací údaje) uložených ve službě IoT Hub, který je spojen s každou instanci modulu.

-   A **dvojče modulu** je dokument JSON uložený ve službě IoT Hub, který obsahuje informace o stavu pro instanci modulu, včetně metadata, konfigurace a podmínky.

-   **Sady SDK** umožňují vyvíjet vlastní moduly v několika jazycích, jako například: C\#, C, Pythonu, Javě a Node.JS.

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>Procesu onboardingu pro modul IoT Edge

Následující snímek obrazovky znázorňuje proces pro modul IoT Edge se veřejné na webu Azure Marketplace.

![Proces certifikace](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>Podrobnosti kroku registrace

-   **Krok 1** – partneři odeslat jejich nabídky s typ nabídky **modul IoT Edge** v nástroji portál partnerů cloudu týmem Azure Marketplace. Musí být oficiální partner společnosti Microsoft pro přístup k nástroji pro portál partnerů cloudu. Další informace najdete v tématu [vydavatele průvodce](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   **Krok 2** – webu Marketplace se automaticky spustí antivirový program a ověří proti malwaru. Tým IoT můžete spustit své vlastní automatizované ingestování testy během této fáze.

-   **Krok 3** – modul je veřejná. Je uveden na webu Marketplace a kontejner může anonymně získaných z adresy URL. Například *marketplace.azurecr.io/module-identifier*.

## <a name="iot-edge-module-certification-criteria"></a>Kritéria pro certifikaci modul IoT Edge

Níže jsou uvedené hlavní požadavky, které musí být splněny pro modul IoT Edge k certifikaci a publikování na webu Azure Marketplace.

>[!Note]
>Tyto požadavky se mohou změnit. Zobrazí se oznámení předem a být zadaný čas aktualizovat své kontejnery tak nebudou splňovat požadavky na aktualizace.

### <a name="technical-requirements"></a>Technické požadavky

**Být modul IoT Edge**

-   Aktuálně jsou podporovány pouze kompatibilní s dockerem kontejnery jako moduly IoT Edge. Modul musí běžet na [Moby](https://mobyproject.org/). 

    >[!Note]
    >Kontejnery dockeru bude pravděpodobně pracovat Moby, protože Moby je základní projekt open source pro Docker.

-   Partner musíte poskytnout následující výchozí nastavení: 

    -   Výchozí **značka** (což nesmí být prázdný.

    -   Výchozí **CreateOptions field** (která může být prázdný.)

    -   Pokud používáte modul IoT SDK, výchozí **dvojčete** (která může být prázdný.)

    -   Pokud používáte modul IoT SDK, výchozí **trasy** (která může být prázdný.)

**Podpora platforem**

-   Pouze platformy, které jsou **obecně k dispozici** v IoT Edge vrstvy 1 (zaznamenaný v [podpory Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)) potřebujete podporovat. Například to aktuálně znamená, že podporuje následující operační systémy a architektury kombinace:

    -   Ubuntu Server 18.04 pro x64

    -   Ubuntu Server 16.04 pro x64

    -   Raspbian stretch pro arm32 (armhf)

**Dimenzování zařízení**

-   Jakékoli zařízení s odpovídající dimenze (CPU/RAM/úložiště/GPU/a atd.) Raspberry Pi nebo vyšší může být zařízení IoT Edge. Pokud modul funguje jenom v rámci konkrétní dimenzi omezení, musí se zadat tato omezení v popisu modulu.

**Výchozí nastavení**

-   Modul by měl začínat výchozích parametrů připravené pro každou podporovanou platformu (operační systém + architektury).

-   Konfigurace nastavení by mělo být jasně popsány (značky, proměnné prostředí, dvojčete, trasy.) V rámci seznamu můžete definovat partneři popis pro jejich modul, který podporuje základní kód HTML nebo odkazuje na externí webovou stránku.

**Správa verzí**

-   Zákazníci musí být schopni vybrat, zda chtějí automatické aktualizace modulu pocházející z webu marketplace, nebo pokud chtějí používat přesnou verzi jsou otestované. Moduly Marketplace musí postupují stejným způsobem správy verzí jako modul runtime IoT Edge. Příklad:

    -   Značek, například "1.0", se zajištěním provozu je možné aktualizovat.

    -   Vedlejší verze aktualizace značek, například "1.3.24", se nedá aktualizovat.

**Telemetrie**

-   Moduly pomocí sady SDK modulu IoT, musíte nastavit jedinečný modul identifikátor přiřazený web Marketplace pro účely telemetrie. To umožňuje identifikovat počet instancí modulu, které běží na Azure Marketplace. Tento jedinečný identifikátor je název kontejneru na webu marketplace na příjem. Použití metod pro následující sady SDK pro nastavení tohoto identifikátoru:
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

-   Pro moduly, které nepoužívají sady SDK modulu IoT jsou k dispozici prostřednictvím portál partnerů cloudu méně přesné insights. Třeba počet souborů ke stažení.

**Zabezpečení**

-   Kontejnery má nejméně privilegovaný přístup k hostiteli, jako je to možné. "Privilegovaných" kontejnery by měla být velmi zřídka.

-   Partneři musí zachovat jejich modul zabezpečení jako součást podpory, kterou poskytují.

**Aktualizace**

-   Partneři musí zachovat jejich modulu aktuální a funkční. Budete upozorněni předem nejsou žádné zásadní změny naplánované na modul Runtime IoT Edge.

**Modul IoT SDK**

-   Včetně sady SDK modulu IoT není předpokladem k certifikaci.
    Včetně sady SDK modulu IoT může poskytovat lepší výkon. Chcete-li například podpora směrování, odesílání zpráv do cloudu a tak dále. Sady SDK modulu IoT je požadováno pro získání telemetrických dat na počet spuštěných instancí modulu.

**Subjektivní požadavek**

-   Musí splňovat aspoň jeden reálného světa případ použití IoT Edge. Například kontejner WordPress by neměl být připojili, pokud zákazník není ochoten pomocí IoT Edge.

**Zákonné požadavky (ze zásad AMP)**

-   Modul musí splňovat smlouvy Microsoft Azure Marketplace a zásady. Další informace najdete v tématu připojené smlouva s vydavatelem a [zásady účasti](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

-   Kromě webu Azure Marketplace smluv a zásady mohou existovat další právní požadavky specifické pro typ nabídky modul IoT Edge. Tato operace právě probíhá kontrola.

-   Modul musí mít *podmínky použití* a *zásady ochrany osobních údajů*.

-   V modulu musí být také třetí strany Všimněte si, že pokud se modul používá jakýmkoli třetím stranám.

**Požadavky na podporu (ze zásad AMP)**

-   Partneři jsou zodpovědní za technickou podporu jejich moduly IoT Edge. Zajistí, aby zůstaly dostupné možnosti podpory uvedené v popisu modul IoT Edge a možnost alespoň jeden podpory je vždy k dispozici. (Viz oddíl 4 smlouvy vydavatele a další podrobnosti.)

**Kategorizace**

-   Všechny moduly IoT Edge se zobrazí v kategorii **Internet věcí \>modul IoT Edge**. Záleží jen na partnera k výběru nejvhodnější dílčí kategorie pro svůj produkt.
