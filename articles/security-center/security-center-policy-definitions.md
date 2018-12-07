---
title: Definice zásady Azure monitorovat ve službě Azure Security Center | Dokumentace Microsoftu
description: Definice zásady Azure ve službě Azure Security Center monitoruje.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: rkarlin
ms.openlocfilehash: e76464e294a3378bf3a275ec4fe7ccbd87c09475
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011626"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Zásady zabezpečení Azure monitorovaný pomocí Azure Security Center
Tento článek obsahuje seznam definic zásad Azure, které můžete sledovat v Security Center.

## <a name="available-security-policies"></a>Dostupné zásady zabezpečení

Informace o tom předdefinovaných zásadách, které jsou monitorovány pomocí služby Security Center, najdete v následující tabulce:

| Zásada | Co zásada dělá |
| --- | --- |
|Povolení auditu diagnostických protokolů ve službě Service Fabric a ve škálovacích sadách virtuálních počítačů|Doporučuje se povolit protokoly, aby se v případě incidentu nebo ohrožení bezpečnosti a jejich vyšetřování daly vysledovat aktivity.|
|Auditovat pravidla ověřování v oborech názvů centra událostí|Event Hub klientů neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. Aby bylo v souladu s nejnižšími oprávněními model zabezpečení, měli byste vytvořit zásady přístupu na úrovni entity pro fronty a témata, které poskytují přístup k jenom na konkrétní entitu.|
|Auditovat existenci pravidel ověřování v entitách centra událostí|Auditovat existenci autorizační pravidla centra událostí entity udělit přístup s nejnižšími.|
|Auditovat neomezený síťový přístup k účtům úložiště|Audit neomezený přístup k síti v nastavení brány firewall na účtu úložiště. Místo toho nakonfigurujte pravidla sítě, abyste účtu úložiště můžou přistupovat jenom aplikace z povolených sítí. Povolit připojení z konkrétní Internetu nebo místní klienty, můžete udělit přístup k provoz z konkrétní služby Azure virtual networks a rozsahy adres veřejné internetové IP.|
|Auditovat používání vlastních pravidel RBAC|Auditovat předdefinované role, jako je například vlastníka, přispěvatele, čtečky místo vlastní role RBAC, které jsou náchylné k chybám. Pomocí vlastních rolí je považován za výjimku a vyžaduje důkladné přezkoumání a modelování hrozeb.|
|Auditovat povolení diagnostických protokolů v Azure Stream Analytics|Povolení protokolů auditu a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat zabezpečený přenos na účty úložiště|Audit požadavek zabezpečený přenos ve vašem účtu úložiště. Zabezpečený přenos je možnost, která vynutí účtu úložiště tak, aby přijímal požadavky jenom ze zabezpečeného připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a služby a chrání přenášená data ze sítě vrstvy útoky man-in-the-middle, odposlouchávání a napadení relace.|
|Auditovat zřizování správce Azure Active Directory pro server SQL|Auditovat zřízení správce Azure Active Directory pro SQL server povolit ověřování Azure AD. Ověřování Azure AD umožňuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft.|
|Auditovat pravidla ověřování v oborech názvů Service Bus|Klienti služby Service Bus neměli používat zásady přístupu na úrovni oboru názvů, který poskytuje přístup ke všem fronty a témata v oboru názvů. Aby bylo v souladu s nejnižšími oprávněními model zabezpečení, měli byste vytvořit zásady přístupu na úrovni entity pro fronty a témata, které poskytují přístup k jenom na konkrétní entitu.|
|Auditovat povolení protokolů diagnostiky ve službě Service Bus|Povolení protokolů auditu a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat nastavení vlastnosti ClusterProtectionLevel v Service Fabricu na EncryptAndSign|Service Fabric nabízí tři úrovně ochrany (None, přihlašování a EncryptAndSign) pro komunikaci mezi uzly pomocí certifikátu primární clusteru. Nastavte úroveň ochrany, k zajištění, že všechny zprávy – uzly jsou zašifrované a digitálně podepsané.| 
|Auditovat používání Azure Active Directory k ověřování klientů v Service Fabricu|Umožňuje auditovat v Service Fabricu používání ověřování klientů jen přes Azure Active Directory.| 
|Auditovat povolení protokolů diagnostiky pro službu Search|Povolení protokolů auditu a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.| 
|Auditovat povolení pouze zabezpečené připojení k mezipaměti Azure Redis|Auditovat povoluje pouze připojení přes protokol SSL pro mezipaměť Azure Redis. Použití zabezpečeného připojení zajišťuje ověřování mezi serverem a služby a chrání data přenášená z síťové vrstvy útoky man-in-the-middle, odposlouchávání a zneužití relace| 
|Auditovat povolení protokolů diagnostiky v Logic Apps|Povolení protokolů auditu a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.| 
|Auditovat povolení protokolů diagnostiky v Key Vaultu|Povolení protokolů auditu a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.|
|Auditovat povolení protokolů diagnostiky v centru událostí|Povolení protokolů auditu a uchovávat až po roce. To umožňuje opětovnému vytvoření aktivit záznamy za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.| 
|Auditovat povolení diagnostických protokolů v Azure Data Lake Store|Povolení protokolů auditu a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.| 
|Auditovat povolení diagnostických protokolů v Data Lake Analytics|Povolení protokolů auditu a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.| 
|Auditovat používání klasických účtů úložiště|Kvůli vylepšení zabezpečení pomocí Azure Resource Manageru pro vaše účty úložiště: silnější access control (RBAC), lepší auditování, nasazení založené na Azure Resource Manageru a zásad správného řízení, přístup ke spravovaným identitám přístup k trezoru klíčů pro tajné klíče , Ověřování azure AD na základě a podpora značek a skupiny prostředků pro snadnější správu zabezpečení.| 
|Auditovat používání klasických virtuálních počítačů|Kvůli vylepšení zabezpečení pomocí Azure Resource Manageru pro vaše virtuální počítače: silnější access control (RBAC), lepší auditování, nasazení založené na Azure Resource Manageru a zásad správného řízení, přístup ke spravovaným identitám přístup k trezoru klíčů pro tajné klíče , Ověřování azure AD na základě a podpora značek a skupiny prostředků pro snadnější správu zabezpečení.| 
|Auditovat konfiguraci pravidel upozornění metriky v účtech Batch|Auditovat konfigurace pravidla upozornění na metriky na účet Batch, aby měla požadované metriky.| 
|Auditovat konfiguraci pravidel upozornění metriky v účtech Batch|Auditovat konfigurace pravidla upozornění na metriky na účet Batch, aby měla požadované metriky.| 
|Auditovat povolení protokolů diagnostiky v účtech Batch|Povolení protokolů auditu a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.| 
|Auditovat povolení šifrování proměnných účtu Automation|Při ukládání citlivých údajů je důležité povolit šifrování prostředků proměnných účtu Automation.| 
|Auditovat povolení protokolů diagnostiky v App Services|Auditovat povolení diagnostických protokolů v aplikaci. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, pokud dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.| 
|Auditování stavu transparentního šifrování dat|Auditování stavu transparentního šifrování dat pro databáze SQL.| 
|Auditovat nastavení auditování SQL na úrovni serveru|Audity existenci auditování na úrovni serveru SQL.| 
|[Preview]: Monitorovat nešifrovanou databázi SQL ve službě Azure Security Center|Nešifrované servery nebo databáze SQL se monitorovat pomocí Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat neauditovanou databázi SQL ve službě Azure Security Center|SQL servery a databáze, které nemají auditování SQL zapnuto bude monitorovat služba Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat chybějící aktualizace systému ve službě Azure Security Center|Chybějící aktualizace systému zabezpečení na vašich serverech bude monitorovat služba Azure Security Center jako doporučení.| 
|[Preview]: Auditovat chybějící šifrování objektů blob pro účty úložiště|Tato zásada Audituje účty úložiště bez šifrování objektů blob. Platí pouze pro typy prostředků Microsoft.Storage, ne pro ostatní poskytovatele úložiště. Je to možné síťový přístup JIT se monitorovat pomocí Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat možný přístup k síti JIT ve službě Azure Security Center|Je to možné síťový přístup pouze In Time (JIT) bude monitorovat služba Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat seznam přípustných povolených aplikací ve službě Azure Security Center|Služba Azure Security Center bude monitorovat konfiguraci seznamu možných seznam povolených aplikací.| 
|[Preview]: Monitorovat benevolentní přístup k síti ve službě Azure Security Center|Skupiny zabezpečení sítě s příliš benevolentními pravidly bude monitorovat služba Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat ohrožení zabezpečení operačního systému ve službě Azure Security Center|Servery, které nesplňují nakonfigurované standardní hodnoty bude monitorovat služba Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat chybějící Endpoint Protection ve službě Azure Security Center|Servery bez nainstalovaného agenta Endpoint Protection bude monitorovat služba Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat nešifrované disky virtuálních počítačů ve službě Azure Security Center|Virtuální počítače bez povoleného šifrování disků bude monitorovat služba Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat ohrožení zabezpečení virtuálních počítačů ve službě Azure Security Center|Monitoruje ohrožení zabezpečení zjištěná řešením Posouzení ohrožení zabezpečení a virtuální počítače, které toto řešení nemají, ve službě Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat nechráněné webové aplikace ve službě Azure Security Center|Webové aplikace bez ochrany firewallu webových aplikací bude monitorovat služba Azure Security Center jako doporučení.| 
|[Preview]: Monitorovat nechráněné koncové body sítě ve službě Azure Security Center|Koncové body sítě bez ochrany firewallem nové generace bude monitorovat služba Azure Security Center jako doporučení.| 
|[Preview]: Monitorování výsledků Posouzení ohrožení zabezpečení SQL ve službě Azure Security Center|Když budete monitorovat výsledky kontroly a doporučení Posouzení ohrožení zabezpečení, získáte informace o tom, jak opravit ohrožení zabezpečení databáze.| 
|[Preview]: Auditování maximálního počtu vlastníků pro předplatné|Pokud chcete omezit riziko porušení zabezpečení ohroženým uživatelem, doporučuje se určit nanejvýš 3 vlastníky předplatných.| 
|[Preview]: Auditování minimálního počtu vlastníků pro předplatné|Aby se zajistila redundance přístupu s oprávněními správce, doporučuje se určit více než jednoho vlastníka předplatného.| 
|[Preview]: Auditování účtů s oprávněními vlastníka v předplatném, které nemají povolené MFA|Pro všechny účty předplatných s oprávněními vlastníka by se měla povolit služba Multi-Factor Authentication (MFA), aby nedošlo k porušení zabezpečení účtů nebo prostředků.| 
|[Preview]: Auditování účtů s oprávněními pro zápis v předplatném, které nemají povolené MFA|Pro všechny účty předplatných s oprávněními pro zápis by se měla povolit služba Mutli-Factor Authentication (MFA), aby nedošlo k porušení zabezpečení účtů nebo prostředků.| 
|[Preview]: Auditování účtů s oprávněními pro čtení v předplatném, které nemají povolené MFA|Pro všechny účty předplatných s oprávněními pro čtení by se měla povolit služba Multi-Factor Authentication (MFA), aby nedošlo k porušení zabezpečení účtů nebo prostředků.| 
|[Preview]: Auditování zastaralých účtů s oprávněními vlastníka v předplatném|Zastaralé účty s oprávněními vlastníka by měla být z předplatného odebrat. Zastaralé účty jsou účty, které byly zablokovány přihlašování.| 
|[Preview]: Auditování zastaralých účtů v předplatném|Zastaralé účty by se měly odebrat z předplatných. Zastaralé účty jsou účty, které byly zablokovány přihlašování.| 
|[Preview]: Auditování externích účtů s oprávněními vlastníka v předplatném|Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněními vlastníka.| 
|[Preview]: Auditování externích účtů s oprávněními pro zápis v předplatném|Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněními pro zápis.| 
|[Preview]: Auditování externích účtů s oprávněními pro čtení v předplatném|Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněními pro čtení.| 




## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Další informace o službě Security Center najdete v následujících článcích:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu pro Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Získejte odpovědi na nejčastější dotazy týkající se použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.

Další informace o Azure Policy najdete v tématu [Co je Azure Policy](../azure-policy/azure-policy-introduction.md).
