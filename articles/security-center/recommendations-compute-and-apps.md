---
title: Azure Security Center doporučení pro počítače & aplikace
description: Azure Security Center doporučení zabezpečení, která vám pomůžou chránit vaše virtuální počítače, počítače, webové aplikace a App Service prostředí.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2019
ms.author: memildin
ms.openlocfilehash: c0cf5951daf004a0c805b688f08d1ccfa4679615
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782161"
---
# <a name="compute-and-app-recommendations---reference-guide"></a>Doporučení k výpočtům a aplikacím – referenční příručka

Tento článek poskytuje úplný seznam doporučení, která se vám můžou zobrazit v Azure Security Center týkající se následujících typů prostředků:

* Virtuální počítače a počítače
* VM Scale Sets
* Cloud Services
* Aplikační služby
* Containers
* Výpočetní prostředky

Vysvětlení, jak tyto funkce Najít a jak je vyřešit, najdete [v tématu.](security-center-virtual-machine-protection.md)

## Doporučení pro výpočty a aplikace<a name="compute-and-app-recs"></a>
|Typ prostředku|Bezpečnostní skóre|Doporučení|Popis|
|----|----|----|----|
|App Service|20|Webová aplikace by měla být přístupná jen přes protokol HTTPS|Omezte přístup k webovým aplikacím jenom přes HTTPS.|
|App Service|20|Function App by měl být přístupný jenom přes HTTPS|Omezte přístup aplikací Function App jenom přes protokol HTTPS.|
|App Service|5|Měly by být povolené diagnostické protokoly v App Services.|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|App Service|10|Vzdálené ladění by mělo být pro webovou aplikaci vypnuté.|Vypnout ladění pro webové aplikace, pokud už je nepotřebujete používat. Vzdálené ladění vyžaduje, aby byly na Function App otevřené porty pro příchozí spojení.|
|App Service|10|Vzdálené ladění by mělo být pro aplikaci funkcí vypnuté.|Vypněte ladění pro Function App, pokud už je nepotřebujete používat. Vzdálené ladění vyžaduje, aby byly na Function App otevřené porty pro příchozí spojení.|
|App Service|10|Nepovolit všem (' * ') prostředkům přístup k aplikaci| Nepovolujte u parametru WEBSITE_LOAD_CERTIFICATES hodnotu. Nastavením parametru na ' ' znamená, že všechny certifikáty budou načteny do osobního úložiště certifikátů vaší webové aplikace. To může vést k zneužití principu nejnižší úrovně oprávnění, protože je pravděpodobné, že lokalita potřebuje mít přístup ke všem certifikátům za běhu.|
|App Service|20|CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím|Povoluje interakci jenom požadovaných domén s webovou aplikací. Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat přístup k vaší webové aplikaci všem doménám.|
|App Service|20|CORS by neměl umožňovat každému prostředku přístup k vašemu Function App| Povoluje interakci jenom požadovaných domén s vaší aplikací funkcí. Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat všem doménám přístup k vaší aplikaci funkcí.|
|Výpočetní prostředky (Batch)|1\. místo|Pravidla upozornění na metriky by měly být nakonfigurovaná na účtech Batch.|Nakonfigurujte pravidla upozornění metrik na účtu Batch a povolte události odstranit kompletní události a odstranit fond. události spuštění odstranění fondu|
|Výpočetní prostředky (Service Fabric)|10|Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.|Ověřování klienta provádějte pouze prostřednictvím Azure Active Directory v Service Fabric.|
|Výpočetní prostředky (účet Automation)|5|Proměnné účtu Automation by se měly šifrovat.|Povoluje šifrování prostředků proměnných účtu služby Automation při ukládání citlivých dat.|
|Výpočetní prostředky (hledání)|5|Auditovat povolení diagnostických protokolů pro služby vyhledávání|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Service Bus)|5|Měly by být povolené diagnostické protokoly v Service Bus.|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Stream Analytics)|5|Měly by být povolené diagnostické protokoly v Azure Stream Analytics.|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Batch)|5|Povolení diagnostických protokolů v účtech Batch|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (centrum událostí)|5|Měly by být povolené diagnostické protokoly v centru událostí.|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Logic Apps)|5|Povolit diagnostické protokoly v Logic Apps|Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě. |
|Výpočetní prostředky (Service Fabric)|15|Nastavte vlastnost ClusterProtectionLevel na EncryptAndSign v Service Fabric|Service Fabric poskytuje tři úrovně ochrany (žádné, podpisové a EncryptAndSign) pro komunikaci mezi uzly pomocí primárního certifikátu clusteru. Nastavte úroveň ochrany tak, aby bylo zajištěno, že všechny zprávy mezi uzly budou šifrovány a digitálně podepsány. |
|Výpočetní prostředky (Service Bus)|1\. místo|Odebrat všechna autorizační pravidla s výjimkou RootManageSharedAccessKey z oboru názvů Service Bus |Klienti Service Bus by neměli používat zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. K zajištění souladu s modelem zabezpečení s minimálními oprávněními byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě.|
|Výpočetní prostředky (centrum událostí)|1\. místo|Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů centra událostí.|Klienti centra událostí by neměli používat zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. K zajištění souladu s modelem zabezpečení s minimálními oprávněními byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě.|
|Výpočetní prostředky (centrum událostí)|5|Měla by být definovaná autorizační pravidla pro entitu centra událostí.|Auditujte autorizační pravidla v entitě centra událostí, abyste udělili přístup s minimálním oprávněním.|
|Počítač|50|Instalace agenta monitorování do počítačů|Nainstalujte agenta monitorování, aby bylo možné na každém počítači povolit shromažďování dat, kontrolu aktualizací, kontrolu základních hodnot a službu Endpoint Protection.|
|Počítač|50|Povolení automatického zřizování a shromažďování dat pro vaše předplatná |Povolením automatického zřizování a shromažďování dat pro počítače ve vašich předplatných umožníte shromažďování dat, kontrolu aktualizací, kontrolu standardních hodnot a službu Endpoint Protection na každém počítači přidávaném do vašich předplatných.|
|Počítač|40|Řešení problémů se stavem agenta monitorování na vašich počítačích|Pokud chcete úplnou Security Center ochranu, vyřešte problémy s agentem monitorování na vašich počítačích podle pokynů v Průvodci odstraňováním potíží.| 
|Počítač|40|Řešení problémů se stavem služby Endpoint Protection na vašich počítačích|V případě úplné ochrany Security Center vyřešte problémy s agentem monitorování na vašich počítačích podle pokynů v Průvodci odstraňováním potíží.|
|Počítač|40|Řešení potíží s chybějícími daty kontroly na vašich počítačích|Řešení potíží s chybějícími daty kontroly na virtuálních počítačích a počítačích Chybějící data kontroly na vašich počítačích mají za následek chybějící vyhodnocení zabezpečení, jako je například skenování aktualizací, kontrola standardních hodnot a chybějící kontrola řešení ochrany koncových bodů.|
|Počítač|40|Do vašich počítačů by se měly nainstalovat aktualizace systému|Pro zabezpečení virtuálních počítačů a počítačů se systémem Windows a Linux nainstalujte chybějící zabezpečení systému a důležité aktualizace.
|Počítač|15|Přidání brány firewall webových aplikací| Nasazením řešení Firewall webových aplikací (WAF) zabezpečíte své webové aplikace. |
|Počítač|40|Aktualizace verze operačního systému pro vaše role cloudové služby|Aktualizujte verzi operačního systému (OS) pro role cloudové služby na nejnovější verzi dostupnou pro vaši rodinu operačních systémů.|
|Počítač|35|Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno|Opravte chyby zabezpečení v konfiguraci zabezpečení na vašich počítačích, abyste je chránili před útoky.|
|Počítač|35|Oprava ohrožení zabezpečení v konfiguraci zabezpečení v kontejnerech|Napravit ohrožení zabezpečení v konfiguraci zabezpečení na počítačích s nainstalovaným Docker pro ochranu před útoky|
|Počítač|25|Povolit adaptivní řízení aplikací|Povolením řízení aplikací můžete řídit, které aplikace se můžou spouštět na virtuálních počítačích umístěných v Azure. To vám pomůže posílit vaše virtuální počítače proti malwaru. Security Center využívá Machine Learning k analýze aplikací spuštěných na každém virtuálním počítači a pomůže vám použít pravidla pro povolení pomocí těchto inteligentních funkcí. Tato možnost zjednodušuje proces konfigurace a správy pravidel povolených aplikací.|
|Počítač|20|Instalace řešení Endpoint Protection na počítačích|Nainstalujte řešení ochrany koncových bodů na virtuální počítače, abyste je chránili před hrozbami a ohroženími zabezpečení.|
|Počítač|20|Restartujte počítače, aby se projevily systémové aktualizace.|Restartujte počítače, aby se projevily aktualizace systému, a zabezpečte počítač před ohrožením zabezpečení.|
|Počítač|15|Na virtuálních počítačích by se mělo použít šifrování disku|Zašifrujte disky virtuálních počítačů pomocí Azure Disk Encryption pro virtuální počítače s Windows i Linux. Azure Disk Encryption (ADE) využívá standardní funkci nástroje BitLocker systému Windows a funkci DM-crypt systému Linux k poskytnutí šifrování operačního systému a datového disku pro zajištění ochrany a ochrany vašich dat a lepšího zabezpečení a dodržování předpisů vaší organizace. závazky v trezoru klíčů zákazníka Azure V případě, že požadavek na dodržování předpisů a zabezpečení vyžaduje, abyste zašifrují data zakončení pomocí šifrovacích klíčů, včetně šifrování dočasného (místně připojeného dočasného) disku, použijte Azure Disk Encryption. Ve výchozím nastavení se Managed Disks standardně šifrují v klidovém stavu pomocí Azure Storage šifrování služby, kde šifrovací klíče jsou spravované klíče Microsoftu v Azure. Pokud to vyhovuje vašim požadavkům na dodržování předpisů a zabezpečení, můžete využít výchozí šifrování spravovaného disku, které splňuje vaše požadavky.|
|Počítač|30|Instalace řešení pro posouzení ohrožení zabezpečení na virtuálních počítačích|Instalace řešení pro posouzení ohrožení zabezpečení na virtuálních počítačích|
|Počítač|15|Přidání brány firewall webových aplikací| Nasazením řešení Firewall webových aplikací (WAF) zabezpečíte své webové aplikace. |
|Počítač|30|Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.|Virtuální počítače, pro které je nasazené řešení zabezpečení od jiných výrobců, se průběžně vyhodnocuje proti chybám zabezpečení aplikací a operačních systémů. Pokaždé, když jsou taková ohrožení zabezpečení zjištěná, jsou k dispozici pro další informace jako součást doporučení.|
|Počítač|30|Instalace řešení pro posouzení ohrožení zabezpečení na virtuálních počítačích|Instalace řešení pro posouzení ohrožení zabezpečení na virtuálních počítačích|
|Počítač|1\. místo|Virtuální počítače by měly být migrovány do nových prostředků AzureRM|Použijte Azure Resource Manager pro vaše virtuální počítače k zajištění vylepšení zabezpečení, jako je: silnější řízení přístupu (RBAC), lepší auditování, nasazení Správce prostředků a zásady správného řízení, přístup ke spravovaným identitám, přístup k trezoru klíčů pro tajné klíče, Ověřování a podpora založené na Azure AD pro značky a skupiny prostředků pro snadnější správu zabezpečení. |
|Počítač|30|Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.|Virtuální počítače, pro které je nasazené řešení zabezpečení od jiných výrobců, se průběžně vyhodnocuje proti chybám zabezpečení aplikací a operačních systémů. Pokaždé, když jsou taková ohrožení zabezpečení zjištěná, jsou k dispozici pro další informace jako součást doporučení.|
|Škálovací sada virtuálních počítačů |4|Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.|Povolte protokoly a zachovejte je po dobu až do roku. To umožňuje znovu vytvořit stopy aktivity pro účely šetření. To je užitečné v případě, že dojde k incidentu zabezpečení nebo dojde k ohrožení bezpečnosti sítě.|
|Škálovací sada virtuálních počítačů|35|V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.|Opravte chyby zabezpečení v konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů, abyste je chránili před útoky. |
|Škálovací sada virtuálních počítačů|5|Napravit selhání stavu ochrany koncových bodů ve virtuálních počítačích služby Virtual Machine Scale Sets|Opravte chyby stavu ochrany koncových bodů v rámci sady škálování virtuálních počítačů, abyste je chránili před hrozbami a ohroženími zabezpečení. |
|Škálovací sada virtuálních počítačů|10|Na virtuálních počítačích by měla být nainstalovaná služba Endpoint Protection.|Nainstalujte řešení ochrany koncových bodů na vaše sady škálování virtuálních počítačů, abyste je chránili před hrozbami a ohroženími zabezpečení. |
|Škálovací sada virtuálních počítačů|40|Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.|Nainstalujte chybějící zabezpečení systému a důležité aktualizace a zabezpečte vaše služby Virtual Machine Scale Sets pro Windows a Linux. |
|


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících tématech:

* [Monitorování identity a přístupu ve službě Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL Service v Azure Security Center](security-center-sql-service-recommendations.md)
