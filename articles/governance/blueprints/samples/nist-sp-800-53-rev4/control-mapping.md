---
title: Ukázkový - SP NIST 800-53 R4 podrobného plánu – mapování ovládacích prvků
description: Ovládací prvek mapování NIST SP 800-53 R4 podrobného plánu ukázky ke službě Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 251dbc396aea5694c4bdec45c194439c9476238b
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226045"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Ovládací prvek mapování NIST SP 800-53 R4 podrobného plánu vzorku

Následující článek podrobně popisuje, jak ukázka Azure plány NIST SP 800-53 R4 podrobného plánu se mapuje na NIST SP 800-53 R4 ovládací prvky. Další informace o ovládacích prvcích najdete v tématu [SP NIST 800-53](https://nvd.nist.gov/800-53).

Následující mapování **SP NIST 800-53 (Rev. 4)** ovládacích prvků. Pomocí navigace na pravé straně můžete přejít přímo na určitý ovládací prvek mapování. Mnohé z namapované ovládací prvky jsou implementovány pomocí [Azure Policy](../../../policy/overview.md) iniciativy. Si Pokud chcete projít kompletní iniciativu, otevřete **zásady** v Azure portal a vyberte **definice** stránky. Poté vyhledejte a vyberte  **\[ve verzi Preview\]: Auditovat NIST SP 800-53 R4 ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování** iniciativa předdefinovaných zásad.

## <a name="ac-2-account-management"></a>Správa účtů AC 2

Tento podrobný plán pomůže budete kontrolovat účty, které nemusí být v souladu s požadavky na správu účtu vaší organizace. Tento podrobný plán přiřadí pět definic Azure Policy, které auditování externí účty s oprávněním pro čtení, zápis a vlastníka na předplatné a zastaralé účty. Kontrolou účty auditováno tyto zásady moci provést vhodnou akci k zajištění, že jsou splněné požadavky na správu účtu.

- \[Ve verzi Preview\]: Auditovat zastaralé účty v rámci předplatného
- \[Ve verzi Preview\]: Auditovat zastaralé účty s oprávněními vlastníka na předplatné
- \[Ve verzi Preview\]: Auditovat externí účty s oprávněními vlastníka na předplatné
- \[Ve verzi Preview\]: Auditovat externí účty s oprávněním pro čtení v rámci předplatného
- \[Ve verzi Preview\]: Auditování externích účtů s oprávněními pro čtení v předplatném

## <a name="ac-2-7-account-management--role-based-schemes"></a>Správa účtů AC-2 (7) | Schémata na základě rolí

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomůžou spravovat, kdo má přístup k prostředkům v Azure. Pomocí webu Azure portal, můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán také přiřadí dva [Azure Policy](../../../policy/overview.md) definice auditovat pomocí ověřování Azure Active Directory pro SQL servery a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft.

- Auditovat zřízení správce Azure Active Directory pro SQL server
- Audit využití služby Azure Active Directory pro ověřování klientů v Service Fabric

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>Správa účtů AC-2 (12) | Účet monitorování / Netypických využití

Just-in-time (JIT) virtuální počítač přístup k uzamčení příchozímu přenosu do virtuálních počítačů Azure, tím omezit vystavení útokům při poskytování snadného přístupu pro připojení k virtuálním počítačům v případě potřeby. Všechny JIT požadavky na přístup k virtuálním počítačům jsou protokolovány v protokolu aktivit umožňuje sledovat využití neobvyklé. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, která vám pomůže monitorovat virtuální počítače, které může podporovat just-in-time přístup, ale dosud nebyly nakonfigurovány.

- \[Ve verzi Preview\]: Monitorovat možné síťový přístup pouze In Time (JIT) ve službě Azure Security Center

## <a name="ac-4-information-flow-enforcement"></a>Vynucení toku informace AC 4

Různé prostředků zdroji (CORS) pro sdílení obsahu můžete povolit App Services prostředky vyžadované z externí domény. Společnost Microsoft doporučuje, že povolíte jenom požadované domén pro interakci s rozhraní API, funkce a webových aplikací. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které vám pomohou monitorovat omezení přístupu prostředky CORS v Azure Security Center.
Pochopení implementace CORS můžete ověřit, že jsou implementovány informace o toku řízení.

- \[Ve verzi Preview\]: Auditovat omezení přístupu prostředku CORS pro webovou aplikaci

## <a name="ac-5-separation-of-duties"></a>AC 5 oddělení povinností

Pro správu redundance neumožňuje mít pouze jeden vlastník předplatného Azure. Naopak s příliš mnoho vlastníků. předplatné Azure může zvýšit riziko porušení zabezpečení přes účet ohroženým. Tento podrobný plán slouží ke správě odpovídající počet vlastníků předplatných Azure přiřazením dvě [Azure Policy](../../../policy/overview.md) definice, které auditování počet vlastníků předplatných Azure. Tento podrobný plán také přiřadí čtyři definic zásad Azure, které vám pomohou řídit členství ve skupině Administrators virtuálních počítačích s Windows. Správa vlastník předplatného a oprávnění Správce virtuálních počítačů vám můžou pomoct implementovat odpovídající oddělení povinností.

- \[Ve verzi Preview\]: Auditovat maximální počet vlastníků pro odběr
- \[Ve verzi Preview\]: Auditovat minimální počet vlastníků pro předplatné
- Audit, že do skupiny správců uvnitř virtuální počítače s Windows vyloučí zadaný členy
- Audit, že skupina Administrators uvnitř virtuálních počítačů Windows obsahuje zadané členy
- Nasazení virtuálního počítače rozšíření auditovat, že do skupiny správců uvnitř virtuální počítače s Windows vyloučí zadaný členy
- Nasazení virtuálního počítače rozšíření auditovat, že skupina Administrators uvnitř virtuálních počítačů Windows obsahuje zadané členy

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>Nejnižší možná oprávnění AC až 6 (7) | Zkontrolujte oprávnění uživatele

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomůžou spravovat, kdo má přístup k prostředkům v Azure. Pomocí webu Azure portal, můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí šest [Azure Policy](../../../policy/overview.md) definice auditovat účty, které by měl být nastaveno jako prioritní ke kontrole. Revize tyto ukazatele účet vám může pomoct Ujistěte se, že jsou implementovány ovládací prvky nejnižších oprávnění.

- \[Ve verzi Preview\]: Auditovat maximální počet vlastníků pro odběr
- \[Ve verzi Preview\]: Auditovat minimální počet vlastníků pro předplatné
- Audit, že do skupiny správců uvnitř virtuální počítače s Windows vyloučí zadaný členy
- Audit, že skupina Administrators uvnitř virtuálních počítačů Windows obsahuje zadané členy
- Nasazení virtuálního počítače rozšíření auditovat, že do skupiny správců uvnitř virtuální počítače s Windows vyloučí zadaný členy
- Nasazení virtuálního počítače rozšíření auditovat, že skupina Administrators uvnitř virtuálních počítačů Windows obsahuje zadané členy

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>Vzdálený přístup AC-17 (1) | Automatické monitorování / ovládací prvek

Tento podrobný plán vám pomůže monitorovat a kontrolovat vzdáleného přístupu tak, že přiřadíte tři [Azure Policy](../../../policy/overview.md) definice pro monitorování, která vzdálené ladění pro aplikace Azure App Service je vypnutý a dvě definice zásad, které auditování systému Linux virtuální počítače, které povolit vzdálená připojení z účtů bez hesla. Tento podrobný plán také přiřadí definici rozhraní Azure Policy, která umožňuje monitorovat neomezený přístup k účtům úložiště. Monitorování tyto ukazatele vám může pomoct zajistit, aby metody vzdáleného přístupu v souladu se zásadami zabezpečení.

- \[Ve verzi Preview\]: Auditování stavu vzdáleného ladění pro aplikace Function App
- \[Ve verzi Preview\]: Auditování stavu vzdáleného ladění pro webovou aplikaci
- \[Ve verzi Preview\]: Auditování stavu vzdáleného ladění pro aplikace API
- \[Ve verzi Preview\]: Auditovat virtuální počítače s Linuxem není povolit vzdálená připojení z účtů bez hesla
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření auditovat, že virtuální počítače s Linuxem nepovoluje vzdálená připojení z účtů bez hesla
- Audit neomezený přístup k síti do účtů úložiště

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Austrálie-3 (2) obsah záznamů auditu | Centralizovaná správa záznam obsahu plánované auditu

Protokol data shromážděná službou Azure Monitor se ukládají v pracovním prostoru Log Analytics umožňuje centralizovanou konfiguraci a správu. Tento podrobný plán vám pomůže zajistit události jsou protokolovány přiřazením sedm [Azure Policy](../../../policy/overview.md) definic, které auditování a vynucování nasazení agenta Log Analytics ve službě Azure virtual machines.

- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Pracovní prostor Log Analytics auditu pro virtuální počítač – neshoda sestavy
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů s Linuxem (VMSS)
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů Windows (VMSS)
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Windows

## <a name="au-5-response-to-audit-processing-failures"></a>Austrálie-5 odpovědi auditovat selhání zpracování

Tento podrobný plán přiřadí pět [Azure Policy](../../../policy/overview.md) definice, které monitorování, auditování a konfigurace protokolování událostí. Monitorování těchto konfigurací můžete poskytovat indikátor neúspěšný audit systému nebo nesprávné konfiguraci a umožňují provést opravné akce.

- \[Ve verzi Preview\]: Monitorovat neauditovanou SQL serverech v Azure Security Center
- Auditování nastavení diagnostiky
- Instance SQL spravované auditu bez rozšířené zabezpečení dat
- Auditování nastavení úrovně auditování SQL serveru.
- Auditování SQL serverů bez rozšířené zabezpečení dat

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>Kontrola auditu AU až 6 (4), analýzy a generování sestav | Centrální revize a analýza

Protokol data shromážděná službou Azure Monitor se ukládají v pracovním prostoru Log Analytics umožňuje centralizovaného generování sestav a analýzy. Tento podrobný plán vám pomůže zajistit události jsou protokolovány přiřazením sedm [Azure Policy](../../../policy/overview.md) definic, které auditování a vynucování nasazení agenta Log Analytics ve službě Azure virtual machines.

- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Pracovní prostor Log Analytics auditu pro virtuální počítač – neshoda sestavy
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů s Linuxem (VMSS)
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů Windows (VMSS)
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Windows

## <a name="au-12-audit-generation"></a>Generování auditování AU až 12.

Tento podrobný plán vám pomůže zajistit systémové události jsou protokolovány přiřazením 15 [Azure Policy](../../../policy/overview.md) definice, které auditování nastavení protokolu pro prostředky Azure. Tyto definice zásad auditování a vynucování nasazení agenta Log Analytics ve službě Azure virtual machines a konfiguraci nastavení auditu pro další typy prostředků Azure. Tyto definice zásad auditu také konfigurace diagnostické protokoly poskytují přehled o operacích, které se provádí v rámci prostředků Azure. Kromě toho auditování a pokročilým zabezpečením dat jsou nakonfigurované na SQL serverech.

- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Pracovní prostor Log Analytics auditu pro virtuální počítač – neshoda sestavy
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů s Linuxem (VMSS)
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů Windows (VMSS)
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Windows
- \[Ve verzi Preview\]: Monitorovat neauditovanou SQL serverech v Azure Security Center
- Zavést nastavení diagnostiky pro skupiny zabezpečení sítě
- Auditování nastavení diagnostiky
- Instance SQL spravované auditu bez rozšířené zabezpečení dat
- Auditování nastavení úrovně auditování SQL serveru.
- Auditování SQL serverů bez rozšířené zabezpečení dat
- Nasazení rozšířeného zabezpečení dat na SQL serverech
- Nasazení auditování na serverech SQL

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>Funkce nejméně CM – 7 (2) | Zabránit spuštění programu

Adaptivní řízení aplikací v Azure Security Center je řešení na seznam povolených pro inteligentní, automatizované začátku do konce aplikace, která může blokovat nebo bránit na virtuálních počítačích s určitým softwarem. Řízení aplikací může spustit v režimu vynucení, která zabrání spuštění neschválené aplikace. Tento podrobný plán přiřadí zásady služby Azure, definice, která vám pomůže monitorovat virtuální počítače, ve kterém se doporučuje seznam povolených aplikací, ale ještě nenakonfigurovalo ověřování.

- \[Ve verzi Preview\]: Monitorovat možné přidávání aplikací na seznam povolených ve službě Azure Security Center

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>Funkce nejméně CM – 7 (5) | Autorizovaný Software / přidávání na seznam povolených

Adaptivní řízení aplikací v Azure Security Center je řešení na seznam povolených pro inteligentní, automatizované začátku do konce aplikace, která může blokovat nebo bránit na virtuálních počítačích s určitým softwarem. Řízení aplikací pomáhá vytvořit seznam schválených aplikací pro vaše virtuální počítače. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, která vám pomůže monitorovat virtuální počítače, ve kterém se doporučuje seznam povolených aplikací, ale ještě nenakonfigurovalo ověřování.

- \[Ve verzi Preview\]: Monitorovat možné přidávání aplikací na seznam povolených ve službě Azure Security Center

## <a name="cm-11-user-installed-software"></a>Uživatel nainstaloval softwaru CM – 11

Adaptivní řízení aplikací v Azure Security Center je řešení na seznam povolených pro inteligentní, automatizované začátku do konce aplikace, která může blokovat nebo bránit na virtuálních počítačích s určitým softwarem. Řízení aplikací pomáhá vynucovat a monitorovat dodržování předpisů se zásadami omezení softwaru. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, která vám pomůže monitorovat virtuální počítače, ve kterém se doporučuje seznam povolených aplikací, ale ještě nenakonfigurovalo ověřování.

- \[Ve verzi Preview\]: Monitorovat možné přidávání aplikací na seznam povolených ve službě Azure Security Center

## <a name="cp-7-alternate-processing-site"></a>Lokalita zpracování alternativní CP-7

Azure Site Recovery replikuje úlohy spuštěné na virtuálních počítačích z primární umístění do sekundárního umístění. Pokud v primární lokalitě dojde k výpadku, převezme zatížení tohoto sekundárního umístění. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, kdy se auditují virtuální počítače bez nakonfigurovat zotavení po havárii. Monitorování tento ukazatel vám může pomoct zajistit potřebné řešení nepředvídaných událostí ovládacích prvků na místě.

- Auditovat virtuální počítače, aniž byste nakonfigurovali zotavení po havárii

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identifikace IA-2 (1) a ověřování (organizace uživatele) | Síťový přístup k privilegovaným účtům

Tento podrobný plán vám pomůže omezit a kontrolovat privilegovaný přístup přiřazením dvě [Azure Policy](../../../policy/overview.md) definice a auditovat účty s vlastníkem a/nebo zápis, které nemají povolené vícefaktorové ověřování. Multi-Factor authentication pomáhá zabezpečit účty i v případě, že dojde k narušení jednu část ověřovacích informací. Díky monitorování účtů bez povolení ověřování službou Multi-Factor Authentication, můžete určit účty, které mohou být pravděpodobně došlo k narušení.

- \[Ve verzi Preview\]: Auditovat účty s oprávněními vlastníka, kteří nejsou v rámci předplatného povolená služba MFA
- \[Ve verzi Preview\]: Auditovat účty s oprávněním pro zápis, které nejsou v rámci předplatného povolená služba MFA

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identifikace IA-2 (2) a ověřování (organizace uživatele) | Přístup k síti pro Neprivilegované účty

Tento podrobný plán vám pomůže omezit a řízení přístupu tak, že přiřadíte [Azure Policy](../../../policy/overview.md) definice auditovat účty s číst oprávnění, které nemají povolené vícefaktorové ověřování. Multi-Factor authentication pomáhá zabezpečit účty i v případě, že dojde k narušení jednu část ověřovacích informací. Díky monitorování účtů bez povolení ověřování službou Multi-Factor Authentication, můžete určit účty, které mohou být pravděpodobně došlo k narušení.

- \[Ve verzi Preview\]: Auditovat účty s oprávněním pro čtení, kteří nejsou v rámci předplatného povolená služba MFA

## <a name="ia-5-authenticator-management"></a>Správa IA-5 Authenticator

Tento podrobný plán přiřadí pět [Azure Policy](../../../policy/overview.md) definice, které audit virtuálních počítačů s Linuxem, které povolit vzdálená připojení z účtů bez hesla a/nebo nesprávná oprávnění nastavena na soubor hesel. Tento podrobný plán také přiřadí definici zásady, kdy se auditují konjugace typ šifrování hesla pro virtuální počítače s Windows. Sledování vám tyto indikátory, je zajistit, že ověřovací data systému souladu se zásadami vaší organizace identifikace a ověřování.

- \[Ve verzi Preview\]: Audit, virtuální počítače s Linuxem nemají účty bez hesla
- \[Ve verzi Preview\]: Nasazení rozšíření virtuálního počítače, virtuální počítače s Linuxem nemají účty bez hesel auditovat
- \[Ve verzi Preview\]: Auditovat, že virtuální počítače s Linuxem mají oprávnění souboru hesel nastavená na 0644
- \[Ve verzi Preview\]: Auditovat, že virtuální počítače s Windows ukládat hesla pomocí reverzibilního šifrování
- \[Ve verzi Preview\]: Nasazení rozšíření virtuálního počítače, virtuální počítače s Linuxem mají oprávnění souboru hesel nastavená na 0644 auditování

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>Správa Authenticator IA-5 (1) | Ověřování pomocí hesla

Tento podrobný plán umožňuje vynucování silných hesel přiřazením 12 [Azure Policy](../../../policy/overview.md) definice, které audit virtuálních počítačů Windows, které nevynucují minimální sílu a další požadavky na heslo. Sledování virtuálních počítačů v rozporu se zásadami sílu hesel umožňuje provést opravné akce k zajištění, že hesla pro všechny uživatelské účty virtuálního počítače v souladu se zásadami vaší organizace heslo.

- \[Ve verzi Preview\]: Auditovat virtuální počítače s Windows nelze opakované použití předchozích hesel 24
- \[Ve verzi Preview\]: Auditovat, že virtuální počítače s Windows mají maximální stáří hesla 70 dní
- \[Ve verzi Preview\]: Auditovat, že máte virtuální počítače s Windows minimální stáří hesla 1 den
- \[Ve verzi Preview\]: Auditovat, že virtuální počítače s Windows mít povolené nastavení složitost hesla
- \[Ve verzi Preview\]: Auditovat, že virtuální počítače s Windows omezení minimální délku hesla a 14 znaky
- \[Ve verzi Preview\]: Auditovat, že virtuální počítače s Windows ukládat hesla pomocí reverzibilního šifrování
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření auditovat virtuální počítače s Windows nelze opakované použití předchozích hesel 24
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření auditovat, že virtuální počítače s Windows mají maximální stáří hesla 70 dní
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření auditovat, že máte virtuální počítače s Windows minimální stáří hesla 1 den
- \[Ve verzi Preview\]: Nasazení rozšíření virtuálního počítače, virtuální počítače s Windows mají povolené nastavení složitost hesel auditovat
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření auditovat, že virtuální počítače s Windows omezení minimální délku hesla a 14 znaky
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření auditovat, že virtuální počítače s Windows ukládat hesla pomocí reverzibilního šifrování

## <a name="ra-5-vulnerability-scanning"></a>Zjišťování ohrožení zabezpečení RA-5

Tento podrobný plán slouží ke správě systému informace o ohrožení zabezpečení přiřazením čtyři [Azure Policy](../../../policy/overview.md) definice, které monitorování ohrožení zabezpečení operačního systému, ohrožení zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center nabízí možnosti vytváření sestav, které vám umožní mít v reálném čase přehled o stavu zabezpečení nasazených prostředků Azure. Tento podrobný plán také přiřadí tři definic zásad, které auditování a vynucování rozšířené zabezpečení dat na SQL serverech. Pokročilé datové zabezpečení zahrnuté sken posouzení ohrožení zabezpečení a možnosti ochrany pokročilých hrozeb vám pomůžou pochopit ohrožení zabezpečení v nasazených prostředků.

- Instance SQL spravované auditu bez rozšířené zabezpečení dat
- Auditování SQL serverů bez rozšířené zabezpečení dat
- Nasazení rozšířeného zabezpečení dat na SQL serverech
- \[Ve verzi Preview\]: Audit ohrožení zabezpečení operačního systému ve virtuálním počítači škálovací nastaví ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorování ohrožení zabezpečení operačního systému ve službě Azure Security Center
- \[Ve verzi Preview\]: Sledovat výsledky posouzení ohrožení zabezpečení SQL ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorování ohrožení zabezpečení virtuálních počítačů ve službě Azure Security Center

## <a name="sc-5-denial-of-service-protection"></a>Odmítnutí služby ochrany SC-5

Azure distribuované s cílem odepření služeb (DDoS) úrovně standard poskytuje další funkce a možnosti snižování rizik při úrovni služby basic. Tyto další funkce zahrnují integraci Azure Monitor a možnost prohlížet si sestavy po útoku omezení rizik. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, která Audituje, jestli je povolené na úrovni standard před útoky DDoS. Princip funkce rozdíl mezi úrovněmi služeb vám může pomoct vybrat nejlepší řešení pro službu ochrany pro prostředí Azure s cílem odepření adres.

- \[Ve verzi Preview\]: Úroveň standard auditu služby DDoS protection je povolená pro virtuální síť

## <a name="sc-7-boundary-protection"></a>Ochrana hranice SC-7

Tento podrobný plán umožňuje řídit a spravovat tak, že přiřadíte hranici systému [Azure Policy](../../../policy/overview.md) definice, která monitoruje skupiny zabezpečení sítě s benevolentními pravidly. Pravidla, která jsou příliš benevolentními může povolit nežádoucí síťový přístup a byste měli zkontrolovat. Tento podrobný plán také přiřadí definici zásady, která monitoruje pro doporučení posílení zabezpečení skupiny zabezpečení sítě v Azure Security Center. Azure Security Center analyzuje vzory provozu aplikace virtual machines směřujících do Internetu a poskytuje doporučení pravidel skupiny chcete snížit potenciální prostor pro napadení zabezpečení sítě.
Kromě toho tento podrobný plán také přiřadí tři definic zásad, které monitorovat nechráněné koncové body, aplikacím a účtům úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem můžete povolit nežádoucí přístup informace obsažené v informačním systému.

- \[Ve verzi Preview\]: Monitorování virtuálních počítačů směřujících k Internetu pro doporučení pro posílení provoz skupina zabezpečení sítě
- \[Ve verzi Preview\]: Monitorovat benevolentní přístup k síti ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorovat nechráněné koncové body sítě ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorovat nechráněné webové aplikace ve službě Azure Security Center
- Audit neomezený přístup k síti do účtů úložiště

## <a name="sc-7-3-boundary-protection--access-points"></a>Ochrana hranice SC-7 (3) | Přístupových bodů

Just-in-time (JIT) virtuální počítač přístup k uzamčení příchozímu přenosu do virtuálních počítačů Azure, tím omezit vystavení útokům při poskytování snadného přístupu pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálním počítačům JIT pomáhá omezit počet externích připojení k vašim prostředkům v Azure. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, která vám pomůže monitorovat virtuální počítače, které může podporovat just-in-time přístup, ale dosud nebyly nakonfigurovány.

- \[Ve verzi Preview\]: Monitorovat možné síťový přístup pouze In Time (JIT) ve službě Azure Security Center

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>Ochrana hranice SC-7 (4) | Externí telekomunikačních služeb

Just-in-time (JIT) virtuální počítač přístup k uzamčení příchozímu přenosu do virtuálních počítačů Azure, tím omezit vystavení útokům při poskytování snadného přístupu pro připojení k virtuálním počítačům v případě potřeby. Přístup k virtuálním počítačům JIT vám pomůže spravovat výjimky zásady toku provozu usnadněním přístupu žádosti a schválení procesy. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, která vám pomůže monitorovat virtuální počítače, které může podporovat just-in-time přístup, ale dosud nebyly nakonfigurovány.

- \[Ve verzi Preview\]: Monitorovat možné síťový přístup pouze In Time (JIT) ve službě Azure Security Center

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>Ochrana informací v klidovém stavu SC-28 (1) | Ochrana kryptografických

Tento podrobný plán umožňuje vynucovat zásady týkající se použití cryptograph ovládacích prvků k ochraně informací v klidovém stavu pomocí přiřazení 9 [Azure Policy](../../../policy/overview.md) slabé kryptografické použití definice, které vynucují konkrétní cryptograph ovládací prvky a auditu nastavení. Principy, kde vašich prostředků Azure může mít optimální kryptografické konfigurace můžete provést opravné akce k zajištění, že prostředky jsou nakonfigurované v souladu se zásadami zabezpečení vašich informací. Konkrétně definice zásad přiřadil Tento podrobný plán vyžadovat šifrování pro účty úložiště data lake; vyžadovat transparentní šifrování dat v databázích SQL; a auditovat chybějící šifrování u databází SQL, disky virtuálních počítačů a proměnných účtu automation.

- \[Ve verzi Preview\]: Monitorovat nešifrované databáze SQL v Azure Security Center
- \[Ve verzi Preview\]: Monitorovat nešifrované disky virtuálních počítačů ve službě Azure Security Center
- Auditovat zabezpečený přenos pro účty úložiště
- Instance SQL spravované auditu bez rozšířené zabezpečení dat
- Auditování SQL serverů bez rozšířené zabezpečení dat
- Auditování stavu transparentního šifrování dat
- Nasazení rozšířeného zabezpečení dat na SQL serverech
- Nasadit SQL DB transparentní šifrování dat
- Vynucení šifrování v účtech Data Lake Store

## <a name="si-2-flaw-remediation"></a>Chyba SI 2 nápravy

Tento podrobný plán slouží ke správě systému vnitropodnikové informace přiřazením šest [Azure Policy](../../../policy/overview.md) definice, které monitorovat chybějící aktualizace systému, ohrožení zabezpečení operačního systému, SQL ohrožení zabezpečení a virtuální počítač ohrožení zabezpečení v Azure Security Center. Azure Security Center nabízí možnosti vytváření sestav, které vám umožní mít v reálném čase přehled o stavu zabezpečení nasazených prostředků Azure. Tento podrobný plán také přiřadí definici zásady, které zajistí automatický upgrade operačního systému pro škálovací sady virtuálních počítačů.

- \[Ve verzi Preview\]: Auditovat všechny chybějící aktualizace systému ve škálovacích sadách virtuálních počítačů ve službě Azure Security Center
- \[Ve verzi Preview\]: Audit ohrožení zabezpečení operačního systému ve virtuálním počítači škálovací nastaví ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorovat chybějící aktualizace systému ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorování ohrožení zabezpečení operačního systému ve službě Azure Security Center
- \[Ve verzi Preview\]: Sledovat výsledky posouzení ohrožení zabezpečení SQL ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorování ohrožení zabezpečení virtuálních počítačů ve službě Azure Security Center
- Vynutit automatický upgrade operačního systému s kontrolami stavu aplikace na VMSS

## <a name="si-3-malicious-code-protection"></a>Škodlivý kód SI 3 ochrany

Tento podrobný plán slouží ke správě ochrany koncových bodů, včetně ochrany škodlivý kód, přiřazením tři [Azure Policy](../../../policy/overview.md) definice, které monitorovat chybějící endpoint protection na virtuálních počítačích v Azure Security Center a Vynuťte antimalwarových řešení od Microsoftu na virtuálních počítačích Windows.

- \[Ve verzi Preview\]: Auditovat řešení ochrany koncových bodů do škálovací sady virtuálních počítačů ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorování chybějící služby Endpoint Protection ve službě Azure Security Center
- Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server

## <a name="si-3-1-malicious-code-protection--central-management"></a>Ochrana škodlivý kód SI-3 (1) | Centrální správy

Tento podrobný plán slouží ke správě ochrany koncových bodů, včetně ochrany škodlivý kód, přiřazením dvě [Azure Policy](../../../policy/overview.md) definice, které monitorovat chybějící endpoint protection na virtuálních počítačích v Azure Security Center. Azure Security Center poskytuje centralizovanou správu a možnosti vytváření sestav, které vám umožní mít v reálném čase přehled o stavu zabezpečení nasazených prostředků Azure.

- \[Ve verzi Preview\]: Auditovat řešení ochrany koncových bodů do škálovací sady virtuálních počítačů ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorování chybějící služby Endpoint Protection ve službě Azure Security Center

## <a name="si-4-information-system-monitoring"></a>Informace SI 4 systém sledování

Tento podrobný plán umožňuje auditování a vynucování protokolování a zabezpečení dat mezi prostředky Azure k monitorování vašeho systému. Konkrétně přiřazené zásady auditu a vynucení nasazení agenta Log Analytics a rozšířené nastavení zabezpečení pro databáze SQL, účty úložiště a síťové prostředky. Tyto funkce vám může pomoci odhalit neobvyklé chování a indikátory útoků, můžete provést příslušnou akci.

- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Pracovní prostor Log Analytics auditu pro virtuální počítač – neshoda sestavy
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů s Linuxem (VMSS)
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů Windows (VMSS)
- \[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Windows
- Instance SQL spravované auditu bez rozšířené zabezpečení dat
- Auditování SQL serverů bez rozšířené zabezpečení dat
- Nasazení rozšířeného zabezpečení dat na SQL serverech
- Nasazení pokročilou ochranu před hrozbami pro účty úložiště.
- Nasazení auditování na serverech SQL
- Nasazení network watcheru při vytvoření virtuálních sítí
- Nasazení detekce hrozeb na SQL serverech

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Systém informací o Incidentech-4 (18) monitorování | Analýza provozu / Skrytějšího průsak ven

Rozšířená ochrana před internetovými útoky pro Azure Storage detekuje a potenciálně nebezpečné pokusy o přístup nebo zneužití účtů úložiště. Ochrana výstrahy obsahují přístup neobvyklé vzory, neobvyklé výpisy/nahrávání a úložiště podezřelé aktivity. Tyto ukazatele vám může pomoci odhalit skrytějšího cílem informace.

- Nasazení pokročilou ochranu před hrozbami pro účty úložiště.

## <a name="next-steps"></a>Další postup

Teď, když jste zkontrolovali mapování ovládacích prvků podrobného plánu R4 SP NIST 800-53, naleznete v následujících článcích se dozvíte o podrobný plán a způsob nasazení této ukázce:

> [!div class="nextstepaction"]
> [Podrobný plán R4 SP NIST 800-53 – přehled](./index.md)
> [SP NIST 800-53 R4 podrobný plán – kroky nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
