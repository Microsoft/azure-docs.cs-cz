---
title: Zabezpečení dat Azure Security Center | Microsoft Docs
description: Tento dokument popisuje způsob správy a ochrany dat v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: 3c63283a1a13e2deb178a53dfc5045d371d6a28a
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996932"
---
# <a name="azure-security-center-data-security"></a>Zabezpečení dat Azure Security Center
Aby zákazníci mohli zabránit, detekovat a reagovat na hrozby, Azure Security Center shromažďuje a zpracovává data související se zabezpečením, včetně informací o konfiguraci, metadat, protokolů událostí, souborů se stavem systému a dalších. Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od kódování po provozování služby.

Tento článek vysvětluje, jak se spravují a chrání data v Azure Security Center.

## <a name="data-sources"></a>Zdroje dat
Azure Security Center analyzuje data z následujících zdrojů, aby poskytovala přehled o stavu zabezpečení, identifikovala ohrožení zabezpečení a doporučuje zmírnění rizik a zjišťoval aktivní hrozby:

- Služby Azure: používá informace o konfiguraci služeb Azure, které jste nasadili, prostřednictvím komunikace s poskytovatelem prostředků této služby.
- Síťový provoz: používá ukázková metadata síťového provozu z infrastruktury Microsoftu, jako je například zdrojová nebo cílová IP adresa/port, velikost paketu a síťový protokol.
- Partnerská řešení: používá výstrahy zabezpečení z integrovaných partnerských řešení, jako jsou brány firewall a antimalwarová řešení.
- Vaše Virtual Machines a servery: používá informace o konfiguraci a informace o událostech zabezpečení, jako jsou protokoly událostí a auditu systému Windows, protokoly IIS, zprávy syslog a soubory s výpisem stavu systému z vašich virtuálních počítačů. Kromě toho, když se vytvoří výstraha, Azure Security Center může vygenerovat snímek ovlivněného disku virtuálního počítače a extrahovat artefakty počítačů související s výstrahou z disku virtuálního počítače, jako je například soubor registru, pro účely forenzní.


## <a name="data-protection"></a>Ochrana dat
**Oddělení dat**: data se v každé součásti v rámci služby udržují logicky odděleně. Všechna data jsou označená v jednotlivých organizacích. Toto označení zůstává v průběhu celého životního cyklu dat a je vymáhání v každé vrstvě služby.

**Přístup k datům**: aby bylo možné poskytnout doporučení pro zabezpečení a prozkoumat potenciální bezpečnostní hrozby, mohou pracovníci společnosti Microsoft získat přístup k informacím shromažďovaným nebo analyzovaným službami Azure, včetně souborů se stavem systému, událostí vytváření procesů, disku virtuálního počítače. snímky a artefakty, které můžou neúmyslně zahrnovat zákaznická data nebo osobní údaje z vašich virtuálních počítačů. V [souladu s podmínkami služby Microsoft Online Services a prohlášením o zásadách ochrany osobních údajů](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), které společnost Microsoft nepoužije k poskytování zákaznických dat ani k odvozování informací o reklamě nebo podobných komerčním účelům. Zákaznická data používáme jenom tak, jak je potřeba k poskytování služeb Azure, včetně účelů, které jsou slučitelné s poskytováním těchto služeb. Zachováváte si všechna práva k zákaznickým datům.

**Použití dat**: Microsoft používá vzory a analýzy hrozeb, které se v různých klientech využívaly k vylepšení možností prevence a detekce; v souladu se závazky ochrany osobních údajů popsané v našem [prohlášení o zásadách ochrany osobních údajů](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Umístění dat

**Vaše pracovní prostory**: pracovní prostor je zadaný pro následující zeměpisných oblastech a data shromážděná z virtuálních počítačů Azure, včetně výpisů stavu systému a některých typů dat výstrah, se ukládají do nejbližšího pracovního prostoru.

| Geografická virtuální počítač                              | Geografické pracovní prostor |
|-------------------------------------|---------------|
| USA, Brazílie, Jižní Afrika | Spojené státy americké |
| Kanada                              | Kanada        |
| Evropa (s výjimkou Spojeného království)   | Evropským        |
| Spojené království                      | Spojené království |
| Asie (s výjimkou Indie, Japonska, Koreje, ČLR)   | Asie a Tichomoří  |
| Korea                              | Asie a Tichomoří  |
| Indie                               | Indie         |
| Japonsko                               | Japonsko         |
| Lidov                               | Lidov         |
| Austrálie                           | Austrálie     |


Snímky disků virtuálních počítačů se ukládají ve stejném účtu úložiště jako disk virtuálního počítače.

Pro virtuální počítače a servery běžící v jiných prostředích, například v místním prostředí, můžete zadat pracovní prostor a oblast, kde jsou shromážděná data uložena.

**Azure Security Center Storage**: informace o výstrahách zabezpečení, včetně partnerských výstrah, se ukládají v oblasti podle umístění souvisejícího prostředku Azure, zatímco jsou uložené informace o stavu zabezpečení a doporučeních. centrálně v USA nebo Evropě podle umístění zákazníka.
Azure Security Center shromažďuje dočasné kopie souborů s výpisem stavu systému a analyzuje je pro legitimaci pokusů o zneužití a úspěšných ohrožení zabezpečení. Azure Security Center provádí tuto analýzu v rámci stejné geografické oblasti jako pracovní prostor a po dokončení analýzy odstraní dočasné kopie.

Artefakty počítačů se ukládají centrálně ve stejné oblasti jako virtuální počítač.


## <a name="managing-data-collection-from-virtual-machines"></a>Správa shromažďování dat z virtuálních počítačů

Když povolíte Security Center v Azure, bude shromažďování dat pro každé z vašich předplatných Azure zapnuté. Shromažďování dat pro vaše předplatná můžete zapnout taky v části zásady zabezpečení Azure Security Center. Když je shromažďování dat zapnuté, Azure Security Center zřídí Microsoft Monitoring Agent na všech stávajících podporovaných virtuálních počítačích Azure a všech nově vytvořených.
Microsoft Monitoring Agent vyhledává různé konfigurace a události související se zabezpečením v [trasování událostí pro Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Kromě toho operační systém během spuštění počítače vyvolá události protokolu událostí. Příklady takových dat: typ a verze operačního systému, protokoly operačního systému (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy, přihlášený uživatel a ID tenanta. Microsoft Monitoring Agent načte položky protokolu událostí a trasování ETW a zkopíruje je do vašich pracovních prostorů pro účely analýzy. Microsoft Monitoring Agent také kopíruje soubory se stavem systému do vašich pracovních prostorů, povoluje události vytváření procesů a povoluje auditování příkazového řádku.

Pokud používáte Azure Security Center Free, můžete také zakázat shromažďování dat z virtuálních počítačů v zásadách zabezpečení. Pro předplatná na úrovni Standard se vyžaduje shromažďování dat. Snímky disků virtuálního počítače a kolekce artefaktů budou pořád povolené i v případě, že je shromažďování dat zakázané.

## <a name="data-consumption"></a>Spotřeba dat

Zákazníci mohou využívat data související s Security Center z různých datových proudů, jak je znázorněno níže:

* **Aktivita Azure**: všechny výstrahy zabezpečení, schválené Security Center požadavky [za běhu](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) a všechny výstrahy vygenerované [adaptivními ovládacími prvky aplikace](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Protokoly Azure monitor**: všechny výstrahy zabezpečení.


> [!NOTE]
> Doporučení zabezpečení je možné také využít prostřednictvím REST API. Další informace najdete v referenčních informacích k [poskytovateli prostředků zabezpečení](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx) pro čtení REST API.

## <a name="see-also"></a>Viz také:
V tomto dokumentu jste zjistili, jak se data spravují a chrání v Azure Security Center. Další informace o Azure Security Center najdete v těchto tématech:

* [Průvodce plánováním a provozem Azure Security Center](security-center-planning-and-operations-guide.md) – Naučte se plánovat a porozumět hlediskům návrhu, které je potřeba přijmout Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav vašich prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Přečtěte si, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak monitorovat stav vašich partnerských řešení.
* [Nejčastější](security-center-faq.md) dotazy k Azure Security Center – Přečtěte si nejčastější dotazy týkající se používání služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si blogové příspěvky o zabezpečení Azure a dodržování předpisů.
