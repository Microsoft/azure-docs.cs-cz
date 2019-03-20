---
title: Definice zásady Azure monitorovat ve službě Azure Security Center | Dokumentace Microsoftu
description: Definice zásady Azure ve službě Azure Security Center monitoruje.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 9d9369afd36f64c27cd2222cab0de5912aa913de
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877492"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Služba Security Center monitorovat zásady zabezpečení Azure
Tento článek obsahuje seznam definic zásad Azure, které můžete sledovat ve službě Azure Security Center. Další informace o zásadách zabezpečení najdete v tématu [práce se zásadami zabezpečení](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Dostupné zásady zabezpečení

Další informace o předdefinovaných zásadách, které jsou monitorovány pomocí služby Security Center, najdete v následující tabulce:

| Zásada | Co zásada dělá |
| --- | --- |
|Povolení auditování diagnostiky protokoly v Azure Service Fabric a škálovací sady virtuálních počítačů|Doporučujeme, abyste povolili protokoly tak, aby byla k dispozici pro zkoumání po incidentu nebo ohrožení protokolu aktivit.|
|Auditovat autorizačních pravidel pro obory názvů služby Event Hubs|Azure Event Hubs klienti neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. Aby bylo v souladu s modelem nejnižších možných oprávnění zabezpečení, měli byste vytvořit zásady přístupu na úrovni entity pro fronty a témata, které poskytují přístup k jenom na konkrétní entitu.|
|Auditovat existenci autorizační pravidla s entitami služby Event Hubs|Auditovat existenci autorizační pravidla s entitami služby Event Hubs pro udělení přístupu s minimálními oprávněními.|
|Auditovat neomezený síťový přístup k účtům úložiště|Audit neomezený přístup k síti v nastavení brány firewall na účtu úložiště. Konfigurace pravidel tak, aby účtu úložiště můžou přistupovat jenom aplikace z povolených sítí. Povolit připojení z konkrétní Internetu nebo místních klientů, udělení přístupu rozsahů IP adres provoz z konkrétní virtuálním sítím Azure nebo do veřejného Internetu.|
|Auditovat používání vlastních pravidel RBAC|Auditovat předdefinované role, jako je například "vlastník, Přispěvatel, čtenář, namísto vlastní přístup na základě rolí (RBAC) ovládací prvek role, které jsou náchylné k chybám. Použití vlastních rolí je považován za výjimku a vyžaduje důkladné přezkoumání a modelování hrozeb.|
|Auditovat povolení diagnostických protokolů v Azure Stream Analytics|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat zabezpečený přenos na účty úložiště|Auditovat požadavky bezpečným přenosem ve vašem účtu úložiště. Zabezpečený přenos je možnost, která vynutí účtu úložiště tak, aby přijímal požadavky jenom ze zabezpečeného připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a služby. Také chrání přenášená data vrstvy před útoky ze sítě, jako je například man-in-the-middle, odposlouchávání a napadení relace.|
|Auditovat zřízení správce Azure Active Directory pro SQL Server|Auditovat zřízení správce Azure Active Directory (Azure AD) pro SQL Server, pokud chcete povolit ověřování Azure AD. Ověřování Azure AD podporuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft.|
|Auditovat pravidla ověřování v oborech názvů Service Bus|Azure Service Bus klienti neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. Aby bylo v souladu s minimálními oprávněními zabezpečení modelem, vytvořte zásady přístupu na úrovni entity pro fronty a témata, které poskytují přístup k jenom na konkrétní entitu.|
|Auditovat povolení protokolů diagnostiky ve službě Service Bus|Povolení protokolů auditu a zajistěte jejich registrace do roku. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat nastavení vlastnosti ClusterProtectionLevel v Service Fabricu na EncryptAndSign|Service Fabric nabízí tři úrovně ochrany pro komunikaci mezi uzly, který používá certifikát primární clusteru: NONE, přihlašování a EncryptAndSign. Nastavte úroveň ochrany, k zajištění, že všechny zprávy – uzly jsou zašifrované a digitálně podepsané.|
|Auditovat používání Azure Active Directory k ověřování klientů v Service Fabricu|Auditovat použití klienta ověřování jenom prostřednictvím Azure AD v Service Fabric.|
|Auditovat povolení protokolů diagnostiky pro službu Search|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat povolení pouze zabezpečené připojení k mezipaměti Azure Redis|Auditovat povolení pouze připojení přes protokol SSL pro mezipaměť Azure Redis. Použití zabezpečeného připojení zajišťuje ověřování mezi serverem a služby. Také chrání přenášená data vrstvy před útoky ze sítě, jako je například man-in-the-middle, odposlouchávání a napadení relace.|
|Auditovat povolení protokolů diagnostiky v Logic Apps|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat povolení protokolů diagnostiky v Key Vaultu|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat povolení diagnostických protokolů ve službě Event Hubs|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat povolení diagnostických protokolů v Azure Data Lake Store|Povolení protokolů auditu a aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat povolení diagnostických protokolů v Data Lake Analytics|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat používání klasických účtů úložiště|Kvůli vylepšení zabezpečení pomocí Azure Resource Manageru pro vaše účty úložiště. Mezi ně patří: <br>-Silnější řízení přístupu (RBAC)<br>-Lépe auditování<br>– Nasazení azure využívající Resource Manager a zásady správného řízení<br>– Přístup ke spravované identity<br>– Přístup k Azure Key Vault pro tajné klíče<br>-Ověřování založené na AD azure<br>– Podpora pro skupiny prostředků pro snadnější správu zabezpečení a značky|
|Auditovat používání klasických virtuálních počítačů|Kvůli vylepšení zabezpečení pomocí Azure Resource Manageru pro vaše virtuální počítače.  Mezi ně patří: <br>-Silnější řízení přístupu (RBAC)<br>-Lépe auditování<br>– Nasazení azure využívající Resource Manager a zásady správného řízení<br>– Přístup ke spravované identity<br>– Přístup k Azure Key Vault pro tajné klíče<br>-Ověřování založené na AD azure<br>– Podpora pro skupiny prostředků pro snadnější správu zabezpečení a značky|
|Auditovat konfiguraci pravidel upozornění metriky v účtech Batch|Auditovat konfigurace pravidla upozornění na metriky na účtů služby Azure Batch umožňuje požadované metriky.|
|Auditovat povolení protokolů diagnostiky v účtech Batch|Povolení protokolů auditu a je pro zajištění aktuálnosti ročně. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat povolení šifrování účtu proměnné služeb automatizace|Je důležité k povolení šifrování proměnných assetů účet Azure Automation při ukládat citlivá data.|
|Auditovat povolení diagnostických protokolů ve službě App Service|Auditovat povolení diagnostických protokolů v aplikaci. Tím se vytvoří záznamy aktivit pro pomoc při dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditování stavu transparentního šifrování dat|Auditování stavu transparentního šifrování dat pro databáze SQL.|
|Auditování nastavení auditování na úrovni serveru SQL|Auditovat existenci auditování na úrovni serveru SQL.|
|\[[Preview]: Monitorovat nešifrovanou databázi SQL ve službě Azure Security Center|Azure Security Center monitoruje nešifrované servery nebo databáze SQL jako doporučená.|
|\[[Preview]: Monitorovat neauditovanou databázi SQL ve službě Azure Security Center|Azure Security Center monitoruje servery SQL Server a databáze, které nemají podle doporučení zapnutého auditování SQL.|
|\[[Preview]: Monitorovat chybějící aktualizace systému ve službě Azure Security Center|Azure Security Center monitoruje chybějící aktualizace systému zabezpečení na vašich serverech, podle doporučení.|
|\[[Preview]: Auditovat chybějící šifrování objektů blob pro účty úložiště|Auditovat účty úložiště, které nepoužívají šifrování objektů blob. To platí jenom pro typy prostředků Microsoft úložiště, ne úložiště od jiných poskytovatelů služeb. Azure Security Center monitoruje možný sítě just-in-time přístup podle doporučení.|
|\[[Preview]: Monitorovat možné síťový přístup just-in-time ve službě Azure Security Center|Azure Security Center monitoruje možný sítě just-in-time přístup podle doporučení.|
|\[[Preview]: Monitorovat možné přidávání aplikací na seznam povolených ve službě Azure Security Center|Azure Security Center monitoruje konfiguraci seznamu povolených IP adres je to možné aplikace.|
|\[[Preview]: Monitorovat benevolentní přístup k síti ve službě Azure Security Center|Azure Security Center monitoruje síťové skupiny zabezpečení, které mají příliš benevolentními pravidly, podle doporučení.|
|\[[Preview]: Monitorování ohrožení zabezpečení operačního systému ve službě Azure Security Center|Azure Security Center monitoruje servery, které není splňují nakonfigurovaným standardním hodnotám, podle doporučení.| 
|\[[Preview]: Monitorování chybějící služby Endpoint Protection ve službě Azure Security Center|Azure Security Center monitoruje servery, které nemají nainstalovaného agenta Microsoft System Center Endpoint Protection, podle doporučení.|
|\[[Preview]: Monitorovat nešifrované disky virtuálních počítačů ve službě Azure Security Center|Azure Security Center monitoruje virtuálních počítačů, které nemají povolené podle doporučení šifrování disku.|
|\[[Preview]: Monitorování ohrožení zabezpečení virtuálních počítačů ve službě Azure Security Center|Monitorování ohrožení zabezpečení, které jsou zjištěny tak, že řešení posouzení ohrožení zabezpečení a virtuální počítače, které nemají řešení posouzení ohrožení zabezpečení v Azure Security Center, podle doporučení.|
|\[[Preview]: Monitorovat nechráněné webové aplikace ve službě Azure Security Center|Azure Security Center monitoruje webové aplikace, které nemají ochrany pomocí brány firewall webových aplikací podle doporučení.|
|\[[Preview]: Monitorovat nechráněné koncové body sítě ve službě Azure Security Center|Azure Security Center monitoruje síťové koncové body, které nemají další ochranu pomocí brány firewall generování podle doporučení.|
|\[[Preview]: Sledovat výsledky posouzení ohrožení zabezpečení SQL ve službě Azure Security Center|Posouzení ohrožení zabezpečení monitorování výsledky kontroly a doporučujeme jak náprava ohrožení zabezpečení databáze.|
|\[[Preview]: Auditovat maximální počet vlastníků pro odběr|Doporučujeme vám, že určíte až tři vlastníkům předplatného umožňuje snížit riziko porušení zabezpečení ohroženým uživatelem.|
|\[[Preview]: Auditovat minimální počet vlastníků pro předplatné|Je vhodné, můžete určit více než jednoho vlastníka předplatného pro zajištění přístupu správce redundance.|
|\[[Preview]: Auditovat účty s oprávněními vlastníka, kteří nejsou v rámci předplatného povolená služba MFA|Vícefaktorové ověřování (MFA) byste měli povolit pro všechny účty předplatných s oprávněními vlastníka k nedošlo k porušení zabezpečení účtů nebo prostředků.|
|\[[Preview]: Auditovat účty s oprávněním pro zápis, které nejsou v rámci předplatného povolená služba MFA|Ověřování službou Multi-Factor Authentication musí být aktivována pro všechny účty předplatných, které mají oprávnění k zápisu do zabránit porušení zabezpečení účtů nebo prostředků.|
|\[[Preview]: Auditovat účty s oprávněním pro čtení, kteří nejsou v rámci předplatného povolená služba MFA|Ověřování službou Multi-Factor Authentication musí být aktivována pro všechny účty předplatných, které mají oprávnění ke čtení pro zabránit porušení zabezpečení účtů nebo prostředků.|
|\[[Preview]: Auditovat zastaralé účty s oprávněními vlastníka na předplatné|Z předplatného by měla odebrat zastaralé účty, které mají oprávnění vlastníka. Zastaralé účty byla zablokována přihlašování.|
|\[[Preview]: Auditovat zastaralé účty v rámci předplatného|Zastaralé účty by se měly odebrat z předplatných. Zastaralé účty byla zablokována přihlašování.|
|\[[Preview]: Auditovat externí účty s oprávněními vlastníka na předplatné|Ze svého předplatného, aby se zabránilo přístupu oprávnění by měl odebrat externí účty, které máte oprávnění vlastníka.|
|\[[Preview]: Audit externí účty s oprávnění k zápisu na předplatné|Externí účty, které mají zápisu oprávnění by měl být odebrána ze svého předplatného, aby nedocházelo k nemonitorovanému přístupu.|
|\[[Preview]: Auditovat externí účty s oprávněním pro čtení v rámci předplatného|Z vašeho předplatného, aby nedocházelo k nemonitorovanému přístupu by měla odebrat externí účty, které máte oprávnění ke čtení.|




## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Další informace o službě Security Center, najdete v následujících článcích.

* [Průvodce Azure Security Center plánováním a provozem](security-center-planning-and-operations-guide.md): Zjistěte, jak naplánovat a pochopit aspekty návrhu ve službě Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md): Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak monitorovat stav partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md): Získejte odpovědi na nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

Další informace o službě Azure Policy najdete v tématu [co je Azure Policy?](../governance/policy/overview.md).
