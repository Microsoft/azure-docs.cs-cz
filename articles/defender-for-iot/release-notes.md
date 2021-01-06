---
title: Co je nového v Azure Defenderu pro IoT
description: Tento článek vám umožní zjistit, co je nového v nejnovější verzi programu Defender pro IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2021
ms.author: shhazam
ms.openlocfilehash: 0281ebcdf1da27513e9b9256b508d911ec7697b5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937636"
---
# <a name="whats-new"></a>Co je nového

Defender pro IoT 10,0 poskytuje vylepšení funkcí, která zlepšují zabezpečení, správu a použitelnost.

## <a name="security"></a>Zabezpečení

Pro tuto verzi byla provedena vylepšení pro obnovení certifikátu a hesla.

### <a name="certificates"></a>Certifikáty
  
Tato verze vám umožní:

- Nahrajte certifikáty SSL přímo do senzorů a místních konzol pro správu.
- Proveďte ověření mezi místní konzolou pro správu a připojenými snímači a mezi konzolou pro správu a konzolou pro správu s vysokou dostupností. Ověřování je založeno na datech vypršení platnosti, na certifikátech kořenové certifikační autority a seznam odvolaných certifikátů.  Pokud se ověření nezdaří, relace nebude pokračovat.

Pro upgrady:

- Během upgradu se nezměnil certifikát SSL ani funkce ověřování.
- Po upgradu můžou správci senzorů a místních správců pro správu nahradit certifikáty SSL nebo aktivovat ověřování certifikátů SSL z nastavení systému a certifikátu SSL.  

Pro nové instalace:

- Při prvním přihlášení se uživatelé musí buď použít certifikát SSL (doporučeno) nebo místně generovaný certifikát podepsaný svým držitelem (nedoporučuje se).
- Ověřování certifikátu je ve výchozím nastavení zapnuté pro nové instalace.

### <a name="password-recovery"></a>Obnovení hesla
  
Správce senzorů a místních konzol pro správu teď může obnovit hesla z Azure Defenderu pro IoT Portal. Předchozí obnovení hesla vyžadovalo zásah týmu podpory.

## <a name="onboarding"></a>Onboarding

### <a name="on-premises-management-console---committed-devices"></a>Místní Konzola pro správu – potvrzená zařízení

Po počátečním přihlášení k místní konzole pro správu se teď uživatelům vyžaduje, aby nahráli aktivační soubor. Tento soubor obsahuje agregovaný počet zařízení, která se mají monitorovat v síti organizace. Toto číslo se označuje jako počet potvrzených zařízení.
Potvrzená zařízení se definují během procesu připojování na portálu Azure Defender pro IoT, kde se vygeneruje aktivační soubor.
Pro nahrání aktivačního souboru se vyžadují první upgrade uživatelů a uživatelů.
Po počáteční aktivaci může počet zařízení zjištěných v síti překročit počet potvrzených zařízení. K této události může dojít například v případě, že se k konzole pro správu připojíte více senzorů. Pokud dojde k nesouladu mezi počtem zjištěných zařízení a počtem potvrzených zařízení, zobrazí se v konzole pro správu upozornění. Pokud k této události dojde, měli byste nahrát nový soubor aktivace.

### <a name="pricing-page-options"></a>Možnosti stránky s cenami

Stránka s cenami umožňuje připojit nové odběry do Azure Defenderu pro IoT a definovat potvrzená zařízení ve vaší síti.  
Stránka s cenami teď také umožňuje spravovat existující předplatná přidružená ke senzoru a aktualizovat závazek zařízení.

### <a name="view-and-manage-onboarded-sensors"></a>Umožňuje zobrazit a spravovat senzory zprovoznění.

Stránka s novým portálem web a senzorů vám umožní:

- Přidejte popisné informace o senzoru. Například zóna přidružená ke senzoru nebo k bezplatným textovým značkám.
- Zobrazení a filtrování informací o senzorech. Můžete například zobrazit podrobnosti o senzorech, které jsou připojené ke cloudu nebo lokálně spravované, nebo zobrazit informace o senzorech v určité zóně.  

## <a name="usability"></a>Použitelnost

### <a name="azure-sentinel-new-connector-page"></a>Stránka nového konektoru služby Azure Sentinel

Změnila se stránka konektoru dat Azure Defender pro IoT ve službě Azure Sentinel. Data Connector je teď založený na předplatných, nikoli v centrech IoT. umožnění zákazníkům lépe spravovat připojení konfigurace do Azure Sentinel.

### <a name="azure-portal-permission-updates"></a>Aktualizace oprávnění Azure Portal  

Byla přidána podpora čtecího modulu zabezpečení a správce zabezpečení.

## <a name="other-updates"></a>Další aktualizace

### <a name="access-group---zone-permissions"></a>Přístup k oprávněním pro zónu skupin
  
Místní pravidla skupiny přístupu konzoly pro správu nebudou zahrnovat možnost udělit přístup ke konkrétní zóně. Neexistují žádné změny v definování pravidel, která používají lokality, oblasti a obchodní jednotky.   Po upgradu budou přístupové skupiny, které obsahují pravidla umožňující přístup ke konkrétním zónám, upraveny tak, aby povolovaly přístup ke své nadřazené lokalitě, včetně všech svých zón.

### <a name="terminology-changes"></a>Terminologické změny

Pojem Asset byl přejmenován na zařízení v senzoru a místní konzole pro správu, v sestavách a v dalších rozhraních řešení.
V okně senzory a místní konzoly pro správu se jako termín Správa této události jmenovaly kroky pro nápravu.

## <a name="next-steps"></a>Další kroky

[Začínáme s Defenderem pro IoT](getting-started.md)
