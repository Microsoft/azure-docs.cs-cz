---
title: Definice zásady Azure monitorovat ve službě Azure Security Center | Dokumentace Microsoftu
description: Definice zásady Azure ve službě Azure Security Center monitoruje.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/19/2019
ms.author: monhaber
ms.openlocfilehash: 25ed9cb624474d5da56d385f4e9c155918ec8eab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428332"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Služba Security Center monitorovat zásady zabezpečení Azure
Tento článek obsahuje seznam definic zásad Azure, které můžete sledovat ve službě Azure Security Center. Další informace o zásadách zabezpečení najdete v tématu [práce se zásadami zabezpečení](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Dostupné zásady zabezpečení

Další informace o předdefinovaných zásadách, které jsou monitorovány pomocí služby Security Center, najdete v následující tabulce:

| Zásada | Co zásada dělá |
| --- | --- |
|Protokoly diagnostiky ve Virtual Machine Scale Sets by měla být povolená.|Doporučujeme, abyste povolili protokoly tak, aby byla k dispozici pro zkoumání po incidentu nebo ohrožení protokolu aktivit.|
|Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měl být odebrány obor názvů centra událostí|Azure Event Hubs klienti neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. Aby bylo v souladu s modelem nejnižších možných oprávnění zabezpečení, měli byste vytvořit zásady přístupu na úrovni entity pro fronty a témata, které poskytují přístup k jenom na konkrétní entitu.|
|Autorizační pravidla centra událostí entity musí být definován.|Auditovat existenci autorizační pravidla s entitami služby Event Hubs pro udělení přístupu s minimálními oprávněními.|
|Přístup k účtům úložišť pomocí brány firewall a konfigurací virtuální sítě by mělo být omezeno|Audit neomezený přístup k síti v nastavení brány firewall na účtu úložiště. Konfigurace pravidel tak, aby účtu úložiště můžou přistupovat jenom aplikace z povolených sítí. Povolit připojení z konkrétní Internetu nebo místních klientů, udělení přístupu rozsahů IP adres provoz z konkrétní virtuálním sítím Azure nebo do veřejného Internetu.|
|Audit využití vlastních pravidel RBAC|Auditovat předdefinované role, jako je například "vlastník, Přispěvatel, čtenář, namísto vlastní přístup na základě rolí (RBAC) ovládací prvek role, které jsou náchylné k chybám. Použití vlastních rolí je považován za výjimku a vyžaduje důkladné přezkoumání a modelování hrozeb.|
|By měly být povolené diagnostické protokoly v Azure Stream Analytics|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Měla by se povolit zabezpečený přenos pro účty úložiště|Auditovat požadavky bezpečným přenosem ve vašem účtu úložiště. Zabezpečený přenos je možnost, která vynutí účtu úložiště tak, aby přijímal požadavky jenom ze zabezpečeného připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a služby. Také chrání přenášená data vrstvy před útoky ze sítě, jako je například man-in-the-middle, odposlouchávání a napadení relace.|
|By mělo proběhnout zřízení správce Azure AD pro SQL server|Auditovat zřízení správce Azure Active Directory (Azure AD) pro SQL Server, pokud chcete povolit ověřování Azure AD. Ověřování Azure AD podporuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft.|
|Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měly odebrat z oboru názvů služby Service Bus|Azure Service Bus klienti neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. Aby bylo v souladu s minimálními oprávněními zabezpečení modelem, vytvořte zásady přístupu na úrovni entity pro fronty a témata, které poskytují přístup k jenom na konkrétní entitu.|
|Protokoly diagnostiky v Service Bus by měla být povolená.|Povolení protokolů auditu a zajistěte jejich registrace do roku. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Vlastnost ClusterProtectionLevel k EncryptAndSign v Service Fabric musí být nastavena|Service Fabric nabízí tři úrovně ochrany pro komunikaci mezi uzly, který používá certifikát primární clusteru: NONE, přihlašování a EncryptAndSign. Nastavte úroveň ochrany, k zajištění, že všechny zprávy – uzly jsou zašifrované a digitálně podepsané.|
|Ověření klienta by měl používat Azure Active Directory|Auditovat použití klienta ověřování jenom prostřednictvím Azure AD v Service Fabric.|
|Diagnostické protokoly v hledání služby by měla být povolená.|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|By měla být povolená pouze zabezpečená připojení k mezipaměti Redis|Auditovat povolení pouze připojení přes protokol SSL pro mezipaměť Azure Redis. Použití zabezpečeného připojení zajišťuje ověřování mezi serverem a služby. Také chrání přenášená data vrstvy před útoky ze sítě, jako je například man-in-the-middle, odposlouchávání a napadení relace.|
|By měly být povolené diagnostické protokoly v Logic Apps|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|By měly být povolené diagnostické protokoly ve službě Key Vault|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|By měly být povolené diagnostické protokoly v Centru událostí|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Protokoly diagnostiky v Azure Data Lake Store by měly být povolené.|Povolení protokolů auditu a aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|By měly být povolené diagnostické protokoly v Data Lake Analytics|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Účty úložiště by se měly migrovat na nové prostředky AzureRM|Kvůli vylepšení zabezpečení pomocí Azure Resource Manageru pro vaše účty úložiště. Mezi ně patří: <br>-Silnější řízení přístupu (RBAC)<br>-Lépe auditování<br>– Nasazení azure využívající Resource Manager a zásady správného řízení<br>– Přístup ke spravované identity<br>– Přístup k Azure Key Vault pro tajné klíče<br>-Ověřování založené na AD azure<br>– Podpora pro skupiny prostředků pro snadnější správu zabezpečení a značky|
|Virtuální počítače by se měly migrovat na nové prostředky AzureRM|Kvůli vylepšení zabezpečení pomocí Azure Resource Manageru pro vaše virtuální počítače.  Mezi ně patří: <br>-Silnější řízení přístupu (RBAC)<br>-Lépe auditování<br>– Nasazení azure využívající Resource Manager a zásady správného řízení<br>– Přístup ke spravované identity<br>– Přístup k Azure Key Vault pro tajné klíče<br>-Ověřování založené na AD azure<br>– Podpora pro skupiny prostředků pro snadnější správu zabezpečení a značky|
|Pravidla upozornění metrik by měla být nakonfigurovaná na účty Batch|Auditovat konfigurace pravidla upozornění na metriky na účtů služby Azure Batch umožňuje požadované metriky.|
|Diagnostické protokoly v účtů služby Batch by měla být povolená.|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|By měl být povolené šifrování proměnných účtu Automation|Je důležité k povolení šifrování proměnných assetů účet Azure Automation při ukládat citlivá data.|
|Diagnostické protokoly v App Services by měla být povolená.|Auditovat povolení diagnostických protokolů v aplikaci. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Měla by se povolit transparentní šifrování dat v databázích SQL|Auditování stavu transparentního šifrování dat pro databáze SQL.|
|Auditování služby SQL server by měla být povolená|Auditovat existenci auditování na úrovni serveru SQL.|
|\[[Preview]: Monitorovat nešifrovanou databázi SQL ve službě Azure Security Center|Azure Security Center monitoruje nešifrované servery nebo databáze SQL jako doporučená.|
|\[[Preview]: Monitorovat neauditovanou databázi SQL ve službě Azure Security Center|Azure Security Center monitoruje servery SQL Server a databáze, které nemají podle doporučení zapnutého auditování SQL.|
|\[[Preview]: Aktualizace systému musí být nainstalován na vašich počítačích.|Azure Security Center monitoruje chybějící aktualizace systému zabezpečení na vašich serverech, podle doporučení.|
|\[[Preview]: Auditovat chybějící šifrování objektů blob pro účty úložiště|Auditovat účty úložiště, které nepoužívají šifrování objektů blob. To platí jenom pro typy prostředků Microsoft úložiště, ne úložiště od jiných poskytovatelů služeb. Azure Security Center monitoruje možný sítě just-in-time přístup podle doporučení.|
|\[[Preview]: Řízení přístupu k síti Just-In-Time bude použito na virtuálních počítačích|Azure Security Center monitoruje možný sítě just-in-time přístup podle doporučení.|
|\[[Preview]: Na virtuálních počítačích musí být aktivována adaptivní řízení aplikací|Azure Security Center monitoruje konfiguraci seznamu povolených IP adres je to možné aplikace.|
|\[[Preview]: Chybějící skupiny zabezpečení sítě pro virtuální počítače by měl být nakonfigurovaný|Azure Security Center monitoruje síťové skupiny zabezpečení, které mají příliš benevolentními pravidly, podle doporučení.|
|\[[Preview]: Měli napravit ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích.|Azure Security Center monitoruje servery, které není splňují nakonfigurovaným standardním hodnotám, podle doporučení.| 
|\[[Preview]: Aplikace Endpoint protection musí být nainstalován na virtuálních počítačích|Azure Security Center monitoruje servery, které nemají nainstalovaného agenta Microsoft System Center Endpoint Protection, podle doporučení.|
|\[[Preview]: Šifrování disků bude použito na virtuálních počítačích|Azure Security Center monitoruje virtuálních počítačů, které nemají povolené podle doporučení šifrování disku.|
|\[[Preview]: Ohrožení zabezpečení by měl být nápravu řešení posouzení ohrožení zabezpečení|Monitorování ohrožení zabezpečení, které jsou zjištěny tak, že řešení posouzení ohrožení zabezpečení a virtuální počítače, které nemají řešení posouzení ohrožení zabezpečení v Azure Security Center, podle doporučení.|
|\[[Preview]: Monitorovat nechráněné webové aplikace ve službě Azure Security Center|Azure Security Center monitoruje webové aplikace, které nemají ochrany pomocí brány firewall webových aplikací podle doporučení.|
|\[[Preview]: Řešení ochrany koncových bodů musí být nainstalován na virtuálních počítačích|Azure Security Center monitoruje síťové koncové body, které nemají další ochranu pomocí brány firewall generování podle doporučení.|
|\[[Preview]: Měli napravit ohrožení zabezpečení na vašich databází SQL|Posouzení ohrožení zabezpečení monitorování výsledky kontroly a doporučujeme jak náprava ohrožení zabezpečení databáze.|
|\[[Preview]: Maximální počet vlastníků 3 by měla být určena pro vaše předplatné|Doporučujeme vám, že určíte až tři vlastníkům předplatného umožňuje snížit riziko porušení zabezpečení ohroženým uživatelem.|
|\[[Preview]: Měla by existovat více než jednoho vlastníka, které jsou přiřazené k vašemu předplatnému|Je vhodné, můžete určit více než jednoho vlastníka předplatného pro zajištění přístupu správce redundance.|
|\[[Preview]: MFA povolena na účty s oprávněními vlastníka v rámci předplatného |Vícefaktorové ověřování (MFA) byste měli povolit pro všechny účty předplatných s oprávněními vlastníka k nedošlo k porušení zabezpečení účtů nebo prostředků.|
|\[[Preview]: Na vaše předplatné účty s oprávněním pro zápis musí být aktivována MFA|Ověřování službou Multi-Factor Authentication musí být aktivována pro všechny účty předplatných, které mají oprávnění k zápisu do zabránit porušení zabezpečení účtů nebo prostředků.|
|\[[Preview]: U vašeho předplatného účty s oprávněním pro čtení by měl zapnuté MFA.|Ověřování službou Multi-Factor Authentication musí být aktivována pro všechny účty předplatných, které mají oprávnění ke čtení pro zabránit porušení zabezpečení účtů nebo prostředků.|
|\[[Preview]: By měla být z předplatného odebrat zastaralé účty s oprávněními vlastníka|Z předplatného by měla odebrat zastaralé účty, které mají oprávnění vlastníka. Zastaralé účty byla zablokována přihlašování.|
|\[[Preview]: By měla být z předplatného odebrat zastaralé účty|Zastaralé účty by měly odebrat z vašich předplatných. Zastaralé účty byla zablokována přihlašování.|
|\[[Preview]: By měla být z předplatného odebrat externí účty s oprávněními vlastníka|Ze svého předplatného, aby se zabránilo přístupu oprávnění by měl odebrat externí účty, které máte oprávnění vlastníka.|
|\[[Preview]: Externí účty s zápisu, že by měla být z předplatného odebrat oprávnění|Externí účty, které mají zápisu oprávnění by měl být odebrána ze svého předplatného, aby nedocházelo k nemonitorovanému přístupu.|
|\[[Preview]: By měla být z předplatného odebrat externí účty s oprávněním pro čtení|Z vašeho předplatného, aby nedocházelo k nemonitorovanému přístupu by měla odebrat externí účty, které máte oprávnění ke čtení.|




## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Další informace o službě Security Center, najdete v následujících článcích.

* [Průvodce Azure Security Center plánováním a provozem](security-center-planning-and-operations-guide.md): Zjistěte, jak naplánovat a pochopit aspekty návrhu ve službě Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md): Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak monitorovat stav partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md): Získejte odpovědi na nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

Další informace o službě Azure Policy najdete v tématu [co je Azure Policy?](../governance/policy/overview.md).
