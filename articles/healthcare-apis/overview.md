---
title: Co je rozhraní API služby Azure pro Preview FHIR – rozhraní API služby Azure pro FHIR ve verzi Preview
description: Tento článek popisuje rozhraní API služby Azure pro FHIR.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 327c28b70c564f7e5099ada14a0d4653b7f0db6c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823476"
---
# <a name="what-is-azure-api-for-fhirreg-preview"></a>Co je Azure API pro FHIR&reg; ve verzi Preview?
Rozhraní API služby Azure pro FHIR umožňuje rychlé výměny dat přes rozhraní API FHIR, se opírá spravované platformy – jako služba (PaaS) nabízí v cloudu. To usnadňuje pro práci s daty stavu pro ingestování, spravovat a každý zachovat Protected Health Information [chráněných zdravotních informací](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) v cloudu: 

- Spravovaná služba FHIR, zřídit během několika minut v cloudu 
- Na podnikové úrovni, na základě FHIR® koncový bod v Azure pro přístup k datům a ukládání ve formátu FHIR®
- Vysoký výkon, nízká latence
- Zabezpečte správu z chráněných dat stavu (PHI) v prostředí vyhovující
- Implementace web a SMART na FHIR pro mobilní zařízení
- Ovládejte svá vlastní data ve velkém měřítku pomocí řízení přístupu na základě rolí (RBAC)
- Protokolu sledování auditu pro přístup, vytváření, úpravu a čtení v rámci každé úložiště dat

Rozhraní API služby Azure pro FHIR umožňuje vytvořit a nasadit službu FHIR během několika minut využití elastického škálování v cloudu.  Platíte jenom za propustnost a úložiště, které potřebujete. Služby Azure, které stojí za pro FHIR rozhraní API služby Azure jsou navržené pro rychlý výkon bez ohledu na to, jakou velikost datové sady, spravujete.  Vrstvu trvalosti dat v Azure API pro FHIR využívá Azure Cosmos DB, která garantuje latenci na úrovni 99. percentilu a vysokou dostupnost s možnostmi multihomingu. 

Rozhraní FHIR API a úložiště dat odpovídající povolit můžete bezpečně připojit a pracovat s všechny systémy, které využívá rozhraní API FHIR.  Společnost Microsoft má o operací a údržby, aktualizace a požadavky na dodržování předpisů do nabídky PaaS, takže můžete uvolnit tak prostředky vlastní vývoj a funkční. 

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>**Využívá širokých možností svá data pomocí FHIR**
Zdravotnictví je možné rychle transformovat data o stavu nově vznikající standardní [FHIR&reg; ](https://hl7.org/fhir) (rychlé Healthcare Interoperability prostředky). FHIR umožňuje robustní a rozšiřitelné datového modelu o standardizované sémantiku a data systému exchange, která umožňuje všechny systémy pomocí FHIR vzájemnou spolupráci.  Transformace dat FHIR umožňuje rychle připojit stávajících zdrojů dat, jako jsou elektronické zdravotní záznamy systémy nebo výzkumu databází. FHIR taky umožňuje rychlé výměny dat v moderních implementací mobile a vývoj pro web. Co je nejdůležitější FHIR můžete zjednodušit příjem dat a zrychlit vývoj aplikací pomocí analýzy a nástrojů strojového učení.  

### <a name="securely-manage-health-data-in-the-cloud"></a>**Bezpečná Správa stavu dat v cloudu**
Rozhraní API služby Azure pro FHIR umožňuje výměnu dat prostřednictvím konzistentní vzhledem k aplikacím, RESTful, FHIR rozhraní API na základě HL7 FHIR specifikace. Spravovaný model PaaS nabídky v Azure se opírá o, také poskytuje škálovatelné a zabezpečené prostředí pro správu a ukládání dat chráněné zdravotní informace (PHI) v nativním formátu FHIR.  

### <a name="free-up-your-resources-to-innovate"></a>**Uvolněte své prostředky na inovace**
Může investovat prostředky vytváření a spouštění služby FHIR, ale s rozhraním Azure API pro FHIR Microsoft trvá na pracovním vytížení operací, údržby, aktualizace a požadavky na dodržování předpisů, povolení k uvolnění vlastní provozní a vývoj prostředky.

### <a name="enable-interoperability-with-fhir"></a>**Povolení spolupráce s FHIR** 
Pomocí rozhraní API služby Azure pro FHIR umožňuje vám připojit u každého systému, který využívá FHIR API pro čtení, zápisu, hledání a další funkce.  Můžete použít jako výkonný nástroj konsolidovat, normalizovat a použít machine learningu klinická data z elektronické zdravotní záznamy, klinickým lékařům a pacientů řídicí panely, vzdálené monitorování aplikací, nebo s databází mimo vašeho systému, které mají FHIR rozhraní API.

### <a name="control-data-access-at-scale"></a>**Řízení přístupu k datům ve velkém měřítku**
Můžete řídit vaše data. Řízení přístupu na základě rolí (RBAC) umožňuje spravovat jak se data ukládají a získávají.  Poskytuje vyšší zabezpečení a snižuje administrativní zátěž, zjistíte, kdo má přístup k datovým sadám, které vytvoříte, podle definice rolí, které vytvoříte pro vaše prostředí.  

### <a name="audit-logs-and-tracking"></a>**Protokoly auditu a sledování**
Rychle sledujte, kam data pomocí protokolů auditu integrované. Sledování přístupu, vytváření, úpravu a čtení v rámci jednotlivých úložišť.

### <a name="secure-your-data"></a>**Zabezpečení dat** 
Ochrana vašich chráněných zdravotních informací díky inteligentním funkcím špičkové zabezpečení.  Data je izolováno na jedinečné databáze na instanci rozhraní API a chránit pomocí převzetí služeb při selhání ve více oblastech. Rozhraní API služby Azure pro FHIR implementuje vrstvami, podrobné obrana a pokročilou ochranu před hrozbami pro vaše data.  

## <a name="applications-for-a-fhir-service"></a>**Aplikace pro službu FHIR**
Klíče nástroje pro interoperabilitu zdravotnických údajů jsou FHIR servery.  Rozhraní API služby Azure pro FHIR je navržená jako rozhraní API a služby, které můžete vytvářet, nasazovat a začít rychle používat.  Jako standardní FHIR rozšíří ve zdravotnictví, případy použití bude dál rozšiřovat, ale některé počáteční zákaznických aplikací, kde je užitečné pro FHIR API služby Azure jsou následující: 

- **Spuštění/IOT a vývoj aplikací:**  Zákazníci vývoj pacienta nebo poskytovatel napjaté aplikace (mobilní nebo webové) můžete využít rozhraní API služby Azure pro FHIR jako plně spravovaná back-end službu. Rozhraní API služby Azure pro FHIR poskytuje cenný materiál v tom, že zákazníci můžou Správa dat a výměna dat v zabezpečené cloudové prostředí navržené pro data o stavu, využijte SMART na pokyny pro implementaci FHIR a povolení jejich technologie, které se budou využívat všechny Zprostředkovatel systémy (například většina EHRs povolili FHIR čtení rozhraní API).   
- **Zdravotní péče ekosystémů:**  Přestože EHRs neexistuje jako primární "zdrojem pravdivých informací" v mnoha klinické nastavení, není, poskytovatelům mít víc databází, které nejsou připojené k sobě navzájem nebo ukládání dat v různých formátech.  Využívá rozhraní API služby Azure pro FHIR jako služba, která je umístěna nad těmito systémy můžete standardizovat data ve formátu FHIR.  Díky výměně dat mezi různými systémy s formátem data konzistentní vzhledem k aplikacím. 

- **Zdroje informací:** Zdravotní péče výzkumní pracovníci bude užitečné FHIR standard v obecné a rozhraní API služby Azure pro FHIR normalizuje data společného datového modelu FHIR a snižuje zatížení pro strojové učení a sdílení dat.
Výměny dat přes rozhraní API služby Azure pro FHIR obsahuje protokoly auditu a ovládací prvky přístupu tohoto nabízejí kontrolu nad toku dat a který má přístup k datových typů. 

## <a name="fhir-from-microsoft"></a>FHIR od Microsoftu

Možnosti FHIR od společnosti Microsoft jsou k dispozici ve dvou konfiguracích:

* Rozhraní API služby Azure pro FHIR – služba PaaS určená v Azure, A snadno zřídit na webu Azure Portal a spravován společností Microsoft.
* FHIR serveru pro Azure – open source projekt, který je možné nasadit do vašeho předplatného Azure, k dispozici na Githubu v https://github.com/Microsoft/fhir-server.

Pro použití případů, které vyžaduje rozšíření nebo přizpůsobení FHIR serveru nebo vyžadují přístup k podkladovým službám, jako je například databáze – bez nutnosti kontaktovat FHIR rozhraní API, vývojářům by měl vybrat Server FHIR open source pro Azure.   Pro implementaci klíč připravené pro produkční prostředí FHIR rozhraní API a back-endová služba, kde trvalá data by měla lze přistupovat pouze prostřednictvím rozhraní API FHIR vývojáři zvolit rozhraní API služby Azure pro FHIR

## <a name="get-started"></a>Začínáme

Pokud chcete začít pracovat se službou FHIR, postupujte podle 5minutového rychlého startu:

* Nasazení otevřete zdrojový Server FHIR pomocí [prostředí PowerShell](fhir-oss-powershell-quickstart.md)

## <a name="next-steps"></a>Další postup

Po nastavení služby FHIR, budou důležité kroky pro konfiguraci a testování služby:

* [Služba Access FHIR pomocí nástroje Postman](access-fhir-postman-tutorial.md)

Je registrovaná ochranná známka společnosti HL7 FHIR a se používá s oprávněním HL7.
