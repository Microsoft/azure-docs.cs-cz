---
title: Azure Policy definitions monitorované v Azure Security Center | Microsoft Docs
description: Tento článek poskytuje seznam definic Azure Policy, které můžete monitorovat v Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 8/22/2019
ms.author: memildin
ms.openlocfilehash: 07d45f7cafa09ef6c5341acd5ffd0914cc56e4fd
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559259"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Zásady zabezpečení Azure monitorované pomocí Security Center
Tento článek poskytuje seznam definic Azure Policy, které můžete monitorovat v Azure Security Center. Další informace o zásadách zabezpečení najdete v tématu [práce se zásadami zabezpečení](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Dostupné zásady zabezpečení

Další informace o předdefinovaných zásadách sledovaných nástrojem Security Center naleznete v následující tabulce:

| Zásady | Co zásada dělá |
| --- | --- |
|Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.|Doporučujeme povolit protokoly, aby byl záznam aktivity k dispozici pro šetření po incidentu nebo ohrožení.|
|Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů centra událostí.|Klienti Azure Event Hubs neměli používat zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. Pro zajištění souladu s modelem zabezpečení s minimálním oprávněním byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě.|
|Měla by být definovaná autorizační pravidla pro entitu centra událostí.|Auditujte existenci autorizačních pravidel Event Hubs entit a udělte přístup k minimálnímu oprávnění.|
|Přístup k účtům úložiště pomocí brány firewall a konfigurací virtuální sítě by měl být omezený.|Auditujte neomezený přístup k síti v nastavení brány firewall účtu úložiště. Nakonfigurujte Síťová pravidla tak, aby k účtu úložiště měly přístup jenom aplikace z povolených sítí. Pokud chcete povolit připojení z určitých internetových nebo místních klientů, udělte přístup k provozu z konkrétních virtuálních sítí Azure nebo do rozsahů veřejných IP adres sítě.|
|Auditovat využití vlastních pravidel RBAC|Místo vlastních rolí řízení přístupu na základě rolí (RBAC), které jsou náchylné k chybám, proveďte audit předdefinovaných rolí, jako je vlastník, přispěvatel, čtenář. Použití vlastních rolí se považuje za výjimku a vyžaduje přísné kontroly a modelování hrozeb.|
|Měly by být povolené diagnostické protokoly v Azure Stream Analytics.|Auditujte povolování protokolů a udržujte je po dobu až do roku. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Zabezpečený přenos do účtů úložiště by měl být povolený.|Požadavky na audit zabezpečeného přenosu v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou. Chrání také data při přenosu z útoků síťové vrstvy, jako jsou například útoky prostředníkem, odposlouchávání a zneužití relace.|
|Je potřeba zřídit správce Azure AD pro SQL Server.|Audit zřizování správce Azure Active Directory (Azure AD) pro SQL Server povolení ověřování Azure AD. Ověřování Azure AD podporuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu.|
|Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů Service Bus|Klienti Azure Service Bus nepoužívají zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. Pokud chcete zajistit, aby se model zabezpečení s nejnižšími oprávněními rovnal, vytvořte zásady přístupu na úrovni entity pro fronty a témata a poskytněte přístup jenom konkrétní entitě.|
|Měly by být povolené diagnostické protokoly v Service Bus.|Auditujte povolování protokolů a udržujte je po dobu až do roku. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Vlastnost ClusterProtectionLevel pro EncryptAndSign v Service Fabric měla být nastavena.|Service Fabric poskytuje tři úrovně ochrany pro komunikaci mezi uzly, které používají primární certifikát clusteru: žádné, podepsat a EncryptAndSign. Nastavte úroveň ochrany tak, aby bylo zajištěno, že všechny zprávy mezi uzly budou šifrovány a digitálně podepsány.|
|Ověřování klienta by mělo používat Azure Active Directory|Auditujte pomocí ověřování klientů jenom přes Azure AD v Service Fabric.|
|Měly by být povolené diagnostické protokoly ve službě Search Services.|Auditujte povolování protokolů a udržujte je po dobu až do roku. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.|Auditovat povolení jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou. Chrání také data při přenosu z útoků síťové vrstvy, jako jsou například útoky prostředníkem, odposlouchávání a zneužití relace.|
|Měly by být povolené diagnostické protokoly v Logic Apps.|Auditujte povolování protokolů a udržujte je po dobu až do roku. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Měly by být povolené diagnostické protokoly v Key Vault.|Auditujte povolování protokolů a udržujte je po dobu až do roku. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Měly by být povolené diagnostické protokoly v centru událostí.|Auditujte povolování protokolů a udržujte je po dobu až do roku. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Měly by být povolené diagnostické protokoly v Azure Data Lake Store.|Auditujte povolování protokolů a udržujte je až do roku. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Měly by být povolené diagnostické protokoly v Data Lake Analytics.|Auditujte povolování protokolů a udržujte je po dobu až do roku. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Účty úložiště by se měly migrovat do nových prostředků AzureRM.|K zajištění vylepšení zabezpečení použijte Azure Resource Manager pro účty úložiště. Mezi ně patří: <br>– Silnější řízení přístupu (RBAC)<br>– Lepší auditování<br>– Nasazení a zásady správného řízení na základě Azure Resource Manager<br>-Přístup ke spravovaným identitám<br>– Přístup k Azure Key Vault tajných kódů<br>– Ověřování pomocí Azure AD<br>– Podpora značek a skupin prostředků pro snadnější správu zabezpečení|
|Virtuální počítače by měly být migrovány do nových prostředků AzureRM|K zajištění vylepšení zabezpečení použijte Azure Resource Manager pro vaše virtuální počítače. Mezi ně patří: <br>– Silnější řízení přístupu (RBAC)<br>– Lepší auditování<br>– Nasazení a zásady správného řízení na základě Azure Resource Manager<br>-Přístup ke spravovaným identitám<br>– Přístup k Azure Key Vault tajných kódů<br>– Ověřování pomocí Azure AD<br>– Podpora značek a skupin prostředků pro snadnější správu zabezpečení|
|Pravidla upozornění na metriky by měly být nakonfigurovaná na účtech Batch.|Audituje konfiguraci pravidel upozornění metrik na účtech Azure Batch, aby se aktivovala požadovaná metrika.|
|V účtech Batch by měly být povolené diagnostické protokoly.|Auditujte povolování protokolů a udržujte je po dobu až do roku. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Šifrování by mělo být povolené u proměnných účtu Automation.|Pokud ukládáte citlivá data, je důležité povolit šifrování prostředků s proměnnými Azure Automation účtu.|
|Měly by být povolené diagnostické protokoly v App Services.|Audituje povolení diagnostických protokolů v aplikaci. Tím se vytvoří stopa aktivity pro účely šetření, pokud dojde k ohrožení zabezpečení nebo dojde k ohrožení vaší sítě.|
|Je třeba povolit transparentní šifrování dat databází SQL.|Auditování stavu transparentního šifrování dat pro databáze SQL.|
|Auditování SQL serveru by mělo být povolené.|Auditujte existenci auditování SQL na úrovni serveru.|
|\[Preview]: monitorovat nešifrované databáze SQL v Azure Security Center|Azure Security Center monitoruje nešifrované servery nebo databáze SQL podle doporučení.|
|\[Preview]: monitorovat neauditované databáze SQL v Azure Security Center|Azure Security Center monitorovat servery a databáze SQL, které nemají zapnuté auditování SQL, podle doporučení.|
|\[Preview]: aktualizace systému by se měly nainstalovat na vaše počítače|Azure Security Center monitorovat chybějící aktualizace systému zabezpečení na serverech podle doporučení.|
|\[Preview]: Auditovat chybějící šifrování objektů BLOB pro účty úložiště|Auditujte účty úložiště, které nepoužívají šifrování objektů BLOB. To platí jenom pro typy prostředků úložiště Microsoft, nikoli pro úložiště od jiných zprostředkovatelů. Azure Security Center monitorovat možný přístup za běhu sítě podle doporučení.|
|\[Preview]: na virtuálních počítačích by se mělo použít řízení přístupu k síti za běhu|Azure Security Center monitorovat možný přístup za běhu sítě podle doporučení.|
|\[Preview]: na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.|Azure Security Center monitoruje možnou konfiguraci seznamu povolených aplikací.|
|\[Preview]: měly by se nakonfigurovat chybějící skupiny zabezpečení sítě pro virtuální počítače.|Azure Security Center monitoruje skupiny zabezpečení sítě, které mají příliš opravňující pravidla, podle doporučení.|
|\[Preview]: v konfiguraci zabezpečení na vašich počítačích by se měla opravit ohrožení zabezpečení|Azure Security Center monitoruje servery, které nevyhovují nakonfigurovanému směrnému plánu, podle doporučení.| 
|\[Preview]: Endpoint Protection by se měla nainstalovat na virtuální počítače|Azure Security Center monitoruje servery, které nemají nainstalovaného agenta Microsoft System Center Endpoint Protection.|
|\[Preview]: na virtuálních počítačích by se mělo použít šifrování disku|Azure Security Center monitoruje virtuální počítače, které nemají povolené šifrování disků, jako doporučené.|
|\[Preview]: ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení|Sledujte ohrožení zabezpečení zjištěná řešením posouzení ohrožení zabezpečení a virtuální počítače, které nemají řešení posouzení ohrožení zabezpečení v Azure Security Center doporučené.|
|\[Preview]: monitorovat nechráněnou webovou aplikaci v Azure Security Center|Azure Security Center monitoruje webové aplikace, u kterých chybí ochrana firewallu webových aplikací, podle doporučení.|
|\[Preview]: řešení Endpoint Protection by se mělo nainstalovat na virtuální počítače|Azure Security Center monitoruje koncové body sítě, které nemají ochranu bránou firewall nové generace (doporučeno).|
|\[Preview]: je potřeba opravit ohrožení zabezpečení vašich databází SQL.|Sledujte výsledky kontroly vyhodnocování ohrožení zabezpečení a doporučte, jak napravit chyby zabezpečení databáze.|
|\[Preview]: pro vaše předplatné by se měla určit maximálně 3 vlastníci.|Doporučujeme, abyste určili až tři vlastníky předplatného, abyste omezili případné porušení zabezpečení napadeného vlastníka.|
|\[Preview]: k vašemu předplatnému by měl být přiřazený víc než jeden vlastník.|Pro zajištění redundance přístupu správce doporučujeme určit více než jednoho vlastníka předplatného.|
|\[Preview]: pro účty s oprávněním vlastníka v předplatném by mělo být povoleno vícefaktorové ověřování. |Vícefaktorové ověřování (MFA) by mělo být povolené u všech účtů předplatného, které mají oprávnění vlastníka, aby se zabránilo narušení účtů nebo prostředků.|
|\[Preview]: vícefaktorové ověřování by mělo být povolené u účtů předplatného s oprávněním k zápisu|U všech účtů předplatného, které mají oprávnění k zápisu, by mělo být povoleno Multi-Factor Authentication, aby nedošlo k porušení účtů nebo prostředků.|
|\[Preview]: vícefaktorové ověřování by mělo být povolené u účtů předplatného s oprávněním ke čtení|U všech účtů předplatného, které mají oprávnění ke čtení, by mělo být povoleno Multi-Factor Authentication, aby nedošlo k porušení účtů nebo prostředků.|
|\[Preview]: zastaralé účty s oprávněním vlastníka by měly být odebrány z vašeho předplatného.|Zastaralé účty, které mají oprávnění vlastníka, by měly být odebrány z vašeho předplatného. U zastaralých účtů bylo zablokováno přihlášení.|
|\[Preview]: zastaralé účty by se měly z vašeho předplatného odebrat|Zastaralé účty by se měly odebírat z vašich předplatných. U zastaralých účtů bylo zablokováno přihlášení.|
|\[Preview]: z předplatného byste měli odebrat externí účty s oprávněním vlastníka.|Externí účty, které mají oprávnění vlastníka, by měly být odebrány z vašeho předplatného, aby nedocházelo k oprávněním.|
|\[Preview]: z předplatného by se měly odebrat externí účty s oprávněními k zápisu.|Externí účty, které mají oprávnění k zápisu, by měly být odebrány z vašeho předplatného, aby nedocházelo k nemonitorovanému přístupu.|
|\[Preview]: z předplatného by se měly odebrat externí účty s oprávněním ke čtení.|Externí účty, které mají oprávnění ke čtení, by se měly z vašeho předplatného odebrat, aby nedocházelo k nemonitorovanému přístupu.|




## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Další informace o Security Center najdete v následujících článcích.

* [Průvodce plánováním a provozem Azure Security Center](security-center-planning-and-operations-guide.md): Naučte se plánovat a pochopit hlediska návrhu v Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Získejte odpovědi na nejčastější dotazy týkající se použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.

Další informace o Azure Policy najdete v tématu [co je Azure Policy?](../governance/policy/overview.md).
