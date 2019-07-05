---
title: Mapování ovládacích prvků – v3.2.1 PCI-DSS podrobného plánu – ukázka
description: Mapování ovládacího prvku vzorku Standard odvětví platebních karet Data zabezpečení v3.2.1 podrobného plánu Azure Policy a RBAC.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540953"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapování ovládacího prvku vzorku PCI-DSS v3.2.1 podrobného plánu

Následující článek podrobně popisuje, jak ukázková plány Azure PCI-DSS v3.2.1 podrobného plánu se mapuje na ovládací prvky v3.2.1 PCI-DSS. Další informace o ovládacích prvcích najdete v tématu [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Následující mapování **PCI-DSS v3.2.1:2018** ovládacích prvků. Pomocí navigace na pravé straně můžete přejít přímo na určitý ovládací prvek mapování. Mnohé z namapované ovládací prvky jsou implementovány pomocí [Azure Policy](../../../policy/overview.md) iniciativy. Si Pokud chcete projít kompletní iniciativu, otevřete **zásady** v Azure portal a vyberte **definice** stránky. Poté vyhledejte a vyberte  **[Preview] auditu PCI v3.2.1:2018 ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování** iniciativa předdefinovaných zásad.

## <a name="132-and-134-boundary-protection"></a>1.3.2 a 1.3.4 ochrany hranic

Tento podrobný plán umožňuje řídit a spravovat tak, že přiřadíte sítě [Azure Policy](../../../policy/overview.md) definice, které monitoruje skupiny zabezpečení sítě s benevolentními pravidly. Pravidla, která jsou příliš benevolentními může povolit nežádoucí síťový přístup a byste měli zkontrolovat. Tento podrobný plán přiřadí jednu definic zásad Azure, které monitorovat nechráněné koncové body, aplikacím a účtům úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem můžete povolit nežádoucí přístup informace obsažené v informačním systému.

- Audit neomezený přístup k síti do účtů úložiště
- Přístup přes internetový koncový bod by mělo být omezeno

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a 4.1 4.1.g, 4.1.h a 6.5.3 kryptografických ochrany

Tento podrobný plán umožňuje vynucovat zásady s použitím ovládacích prvků cryptograph přiřazením [Azure Policy](../../../policy/overview.md) definice, které vynucují konkrétní cryptograph ovládací prvky a auditu použití nastavení slabého šifrování. Principy, kde vašich prostředků Azure může mít optimální kryptografické konfigurace můžete provést opravné akce k zajištění, že prostředky jsou nakonfigurované v souladu se zásadami zabezpečení vašich informací. Konkrétně zásady přiřadil Tento podrobný plán vyžadují transparentní šifrování dat v databázích SQL; Auditovat chybějící šifrování na účty úložiště a proměnných účtu automation. Existují také zásady, která adresa auditu nezabezpečené připojení k účtům úložiště, aplikace Function App, webové aplikace, aplikace API a Redis Cache a auditu nešifrovaná komunikace Service Fabric.

- Funkce aplikace by měla být přístupná jen přes HTTPS
- Webová aplikace by měla být přístupná jen přes HTTPS
- Aplikace API musí být dostupné jenom přes protokol HTTPS
- Monitorovat nešifrovanou databázi SQL ve službě Azure Security Center
- Šifrování disků bude použito na virtuálních počítačích
- Proměnné služeb automatizace účet by se měla šifrovat
- By měla být povolená pouze zabezpečená připojení k mezipaměti Redis
- Měla by se povolit zabezpečený přenos pro účty úložiště
- Clustery Service Fabric by měla mít nastavenou vlastnost ClusterProtectionLevel k EncryptAndSign
- Měla by se povolit transparentní šifrování dat v databázích SQL
- Nasadit SQL DB transparentní šifrování dat

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 a 11.2.1 zjišťování ohrožení zabezpečení a aktualizace systému

Tento podrobný plán slouží ke správě systému informace o ohrožení zabezpečení přiřazením [Azure Policy](../../../policy/overview.md) definice, které monitorovat chybějící aktualizace systému, ohrožení zabezpečení operačního systému, SQL ohrožení zabezpečení a virtuální počítač ohrožení zabezpečení v Azure Security Center. Azure Security Center nabízí možnosti vytváření sestav, které vám umožní mít v reálném čase přehled o stavu zabezpečení nasazených prostředků Azure.

- Monitorování chybějící služby Endpoint Protection ve službě Azure Security Center
- Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server
- Nasazení detekce hrozeb na SQL serverech
- Aktualizace systému musí být nainstalován na vašich počítačích.
- Měli napravit ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích.
- Měli napravit ohrožení zabezpečení na vašich databází SQL
- Ohrožení zabezpečení by měl být nápravu řešení posouzení ohrožení zabezpečení

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 a 7.1.3 oddělení povinností

Pro správu redundance neumožňuje mít pouze jeden vlastník předplatného Azure. Naopak s příliš mnoho vlastníků. předplatné Azure může zvýšit riziko porušení zabezpečení přes účet ohroženým. Tento podrobný plán slouží ke správě odpovídající počet vlastníků předplatných Azure tak, že přiřadíte [Azure Policy](../../../policy/overview.md) definice, které auditování počet vlastníků předplatných Azure. Správa oprávnění vlastníka předplatného vám můžou pomoct implementovat odpovídající oddělení povinností.

- Měla by existovat více než jednoho vlastníka, které jsou přiřazené k vašemu předplatnému
- Maximální počet vlastníků 3 by měla být určena pro vaše předplatné 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a a 8.3.1.b správy práv privilegovaného přístupu

Tento podrobný plán vám pomůže omezit a řídit práva privilegovaného přístupu přiřazením [Azure Policy](../../../policy/overview.md) definice auditovat externí účty s vlastníkem, zapisovat a číst oprávnění a účty pro zaměstnance s vlastníkem a/nebo zápis oprávnění, které nemají povolené vícefaktorové ověřování. Azure implementuje řízení přístupu na základě rolí (RBAC) pro správu, kdo má přístup k prostředkům Azure. Principy, kde jsou implementovat vlastní pravidla RBAC můžete ověřit potřebujete a správnou implementaci vlastní pravidla RBAC jsou náchylné k chybám. Tento podrobný plán také přiřadí [Azure Policy](../../../policy/overview.md) definice auditovat pomocí ověřování Azure Active Directory pro servery SQL. Pomocí Azure Active Directory authentication zjednodušuje správu oprávnění a centralizuje správu identit uživatelů databáze a ostatní společnosti Microsoft  
Služby.
 
- By měla být z předplatného odebrat externí účty s oprávněními vlastníka
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis
- By měla být z předplatného odebrat externí účty s oprávněním pro čtení
- MFA povolena na účty s oprávněními vlastníka v rámci předplatného
- Vícefaktorové ověřování musí být povolené účty s oprávněním pro zápis v předplatném
- MFA povolena na účty s oprávněním pro čtení v rámci předplatného
- By mělo proběhnout zřízení správce Azure Active Directory pro servery SQL
- Audit využití vlastních pravidel RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 a 8.1.5 minimální oprávnění a přehled uživatele přístupová práva

Azure implementuje na základě rolí řízení přístupu (RBAC) k pomáhá, která spravujete, kdo má přístup k prostředkům v Azure. Pomocí webu Azure portal, můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice auditovat účty, které by měl být nastaveno jako prioritní ke kontrole, včetně odepsanou účtů a externí účty se zvýšenými oprávněními.

- By měla být z předplatného odebrat zastaralé účty
- By měla být z předplatného odebrat zastaralé účty s oprávněními vlastníka
- By měla být z předplatného odebrat externí účty s oprávněními vlastníka
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis
- By měla být z předplatného odebrat externí účty s oprávněním pro čtení

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 odebrání nebo úpravu přístupová práva

Azure implementuje řízení přístupu na základě rolí (RBAC), které vám pomůžou spravovat, kdo má přístup k prostředkům v Azure. Pomocí Azure Active Directory a RBAC, můžete aktualizovat role uživatele tak, aby odrážely změn v organizaci. Pokud je nepotřebujete, účtů může blokovat přihlášení (nebo odstranit), která okamžitě Odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice auditovat odepsanou účet, který by měl být pro odebrání.

- By měla být z předplatného odebrat zastaralé účty
- By měla být z předplatného odebrat zastaralé účty s oprávněními vlastníka

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b a 8.2.5 ověřování pomocí hesla

Tento podrobný plán umožňuje vynucování silných hesel přiřazením [Azure Policy](../../../policy/overview.md) definice, které auditovat virtuální počítače s Windows, který nevynucují minimální sílu a další požadavky na heslo. Sledování virtuálních počítačů v rozporu se zásadami sílu hesel umožňuje provést opravné akce k zajištění, že jsou kompatibilní s zásady hesla pro všechny uživatelské účty virtuálního počítače.

- [Preview]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Preview]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Preview]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Preview]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Preview]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [Preview]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 a 10.5.4 auditovat generování

Tento podrobný plán vám pomůže zajistit systémové události jsou protokolovány přiřazením [Azure Policy](../../../policy/overview.md) definice, které auditování nastavení protokolu pro prostředky Azure.
Diagnostické protokoly poskytují přehled o operacích provedených v rámci prostředků Azure. Protokoly Azure využívají k vytvoření záznamu čas korelační události napříč prostředky synchronizovat interní hodiny.

- Monitorovat neauditovanou SQL serverech v Azure Security Center
- Auditování nastavení diagnostiky
- Auditování nastavení úrovně auditování SQL serveru.
- Nasazení auditování na serverech SQL
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manageru
- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manageru

## <a name="1236-and-1237-information-security"></a>12.3.6 a 12.3.7 zabezpečení informací

Tento podrobný plán umožňuje spravovat a řídit síť přiřazením [Azure Policy](../../../policy/overview.md) definice, které auditování přijatelné síťová umístění a produkty schválené společnosti povoleno pro prostředí. Toto jsou přizpůsobitelné každou společností prostřednictvím parametrů zásad v rámci každé z těchto zásad.

- Povolená umístění
- Povolená umístění pro skupiny prostředků

## <a name="next-steps"></a>Další postup

Teď, když jste zkontrolovali mapování ovládacích prvků podrobného plánu v3.2.1 PCI-DSS, naleznete v následujících článcích najdete informace o přehledu a jak nasadit tuto ukázku:

> [!div class="nextstepaction"]
> [Podrobný plán v3.2.1 PCI-DSS – přehled](./index.md)
> [PCI-DSS v3.2.1 podrobný plán – kroky nasazení](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
