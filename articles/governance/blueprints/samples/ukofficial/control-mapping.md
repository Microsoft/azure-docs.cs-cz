---
title: Ukázka – UK-OFFICIAL podrobného plánu – mapování ovládacích prvků
description: Mapování ovládacích prvků UK-OFFICIAL vzorku podrobného plánu.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2eb6e62bc891a147a89107f5e3de7c2b605bbd09
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276974"
---
# <a name="control-mapping-of-the-uk-official-blueprint-sample"></a>Mapování ovládacích prvků UK-OFFICIAL vzorku podrobného plánu

Následující článek podrobně popisuje, jak Velká Británie oficiální ukázky podrobného plánu se mapuje na UK-OFFICIAL ovládací prvky.
Další informace o ovládacích prvcích najdete v tématu [UK-OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications).

Následující mapování **UK-OFFICIAL** ovládacích prvků. Pomocí navigace na pravé straně můžete přejít přímo na určitý ovládací prvek mapování. Mnohé z namapované ovládací prvky jsou implementovány pomocí [Azure Policy](../../../policy/overview.md) iniciativy. Si Pokud chcete projít kompletní iniciativu, otevřete **zásady** v Azure portal a vyberte **definice** stránky. Poté vyhledejte a vyberte  **[Preview] auditu UK oficiální ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování** iniciativa předdefinovaných zásad.

## <a name="1-data-in-transit-protection"></a>1 data Protection přenosu

Podrobný plán vám pomůže zajistit přenosu informací pomocí služeb Azure je zabezpečený přiřazením dvě [Azure Policy](../../../policy/overview.md) definice auditovat nezabezpečené připojení k účtům úložiště a Redis Cache.

- By měla být povolená pouze zabezpečená připojení k mezipaměti Redis
- Měla by se povolit zabezpečený přenos pro účty úložiště

## <a name="23-data-at-rest-protection"></a>2.3 data na zbývající ochrany

Tento podrobný plán umožňuje vynucovat zásady týkající se použití ovládacích prvků cryptograph přiřazením 11 [Azure Policy](../../../policy/overview.md) definice, které vynucují konkrétní cryptograph ovládací prvky a auditu použití nastavení slabého šifrování.
Principy, kde vašich prostředků Azure může mít optimální kryptografické konfigurace můžete provést opravné akce k zajištění, že prostředky jsou nakonfigurované v souladu se zásadami zabezpečení vašich informací. Konkrétně zásady přiřazené službou Tento podrobný plán vyžadovat šifrování pro účty úložiště data lake; vyžadovat transparentní šifrování dat v databázích SQL; Auditovat chybějící šifrování na účty úložiště, databáze SQL, disky virtuálních počítačů a proměnných účtu automation; Auditovat nezabezpečené připojení k účtům úložiště a Redis Cache Auditovat šifrování hesla malý virtuální počítač; a auditovat nešifrovaná komunikace Service Fabric.

- Monitorovat nešifrované databáze SQL v Azure Security Center
- Šifrování disků bude použito na virtuálních počítačích
- Proměnné služeb automatizace účet by se měla šifrovat
- Měla by se povolit zabezpečený přenos pro účty úložiště
- Měla by se povolit zabezpečený přenos pro účty úložiště
- Clustery Service Fabric by měla mít nastavenou vlastnost ClusterProtectionLevel k EncryptAndSign
- Měla by se povolit transparentní šifrování dat v databázích SQL
- Nasadit SQL DB transparentní šifrování dat
- Vyžadovat šifrování u účtů Data Lake Store
- Povolená umístění (bylo obtížné naprogramovány tak, aby "Velká Británie – JIH" a "Velká Británie – ZÁPAD")
- Povolená umístění pro skupiny prostředků (bylo obtížné naprogramovány tak, aby "Velká Británie – JIH" a "Velká Británie – ZÁPAD")

## <a name="52-vulnerability-management"></a>5.2 Správa ohrožení zabezpečení

Tento podrobný plán slouží ke správě systému informace o ohrožení zabezpečení přiřazením pět [Azure Policy](../../../policy/overview.md) definice, které monitorovat chybějící endpoint protection, chybějící aktualizace systému, operační systém, ohrožení zabezpečení SQL ohrožení zabezpečení a ohrožení zabezpečení virtuálního počítače. Tyto přehledy poskytují v reálném čase informace o stavu zabezpečení nasazených prostředků a můžete určit prioritu nápravné akce.

- Monitorování chybějící služby Endpoint Protection ve službě Azure Security Center
- Aktualizace systému musí být nainstalován na vašich počítačích.
- Měli napravit ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích.
- Měli napravit ohrožení zabezpečení na vašich databází SQL
- Ohrožení zabezpečení by měl být nápravu řešení posouzení ohrožení zabezpečení

## <a name="53-protective-monitoring"></a>5.3 ochranná monitorování

Tento podrobný plán vám pomůže chránit informace prostředků systému přiřazením [Azure Policy](../../../policy/overview.md) definice, která monitoruje neomezené účty úložiště. Tento podrobný plán také přiřadí definici rozhraní Azure Policy, která monitoruje aktivity seznamu povolených IP adres.

- Audit neomezený přístup k síti do účtů úložiště
- Na virtuálních počítačích musí být aktivována adaptivní řízení aplikací
- Nasazení detekce hrozeb na SQL serverech
- Nasadit výchozí rozšíření Microsoft IaaS malwaru pro Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 zabezpečení, Správa uživatelů / 10 identita a ověřování

Azure implementuje na základě rolí řízení přístupu (RBAC) k pomáhá, která spravujete, kdo má přístup k prostředkům v Azure. Pomocí webu Azure portal, můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán vám pomůže omezit a řídit práva privilegovaného přístupu přiřazením šest [Azure Policy](../../../policy/overview.md) definice a auditovat externí účty s vlastníkem a/nebo čtení/zápis oprávnění a účty s vlastníkem, čtení a zápis oprávnění, které nemají povolené vícefaktorové ověřování.

- MFA povolena na účty s oprávněními vlastníka v rámci předplatného
- Vícefaktorové ověřování musí být povolené účty s oprávněním pro zápis v předplatném
- MFA povolena na účty s oprávněním pro čtení v rámci předplatného
- Auditovat externí účty s oprávněními vlastníka na předplatné
- Auditování externích účtů s oprávněními pro čtení v předplatném
- Auditovat externí účty s oprávněním pro čtení v rámci předplatného

Tento podrobný plán přiřadí dvě definice zásad Azure a kontrolovat použití ověřování pomocí Azure Active Directory pro SQL servery a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft.

- By mělo proběhnout zřízení správce Azure Active Directory pro servery SQL
- Clustery Service Fabric měli používat jenom služby Azure Active Directory pro ověřování klientů

Tento podrobný plán také přiřadí pět definic Azure Policy auditovat účty, které by měl být nastaveno jako prioritní ke kontrole, včetně odepsanou a externí účty. Pokud je nepotřebujete, účtů může blokovat přihlášení (nebo odstranit), která okamžitě Odebere přístupová práva k prostředkům Azure. Tento podrobný plán dvěma definicemi zásad Azure přiřadí auditu odpisy účet, který by měl být pro odebrání.

- By měla být z předplatného odebrat zastaralé účty
- By měla být z předplatného odebrat zastaralé účty s oprávněními vlastníka
- By měla být z předplatného odebrat externí účty s oprávněními vlastníka
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis

Tento podrobný plán také přiřadí definici rozhraní Azure Policy, kdy se auditují virtuální počítač s Linuxem heslo souboru oprávnění pro výstrahy v případě, že máte správně nastavené. Tento návrh umožňuje provádět nápravné akce, která Ujistěte se, že nebudou ohroženy ověřovací data.

- [Preview]: Audit Linux VM /etc/passwd file permissions are set to 0644

Tento podrobný plán umožňuje vynucování silných hesel přiřazením 10 definice zásad Azure, které auditovat virtuální počítače s Windows, který nevynucují minimální sílu a další požadavky na heslo. Sledování virtuálních počítačů v rozporu se zásadami sílu hesel umožňuje provést opravné akce k zajištění, že jsou kompatibilní s zásady hesla pro všechny uživatelské účty virtuálního počítače.

- [Preview]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [Preview]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Preview]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [Preview]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Preview]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [Preview]: Audit Windows VMs that do not have the password complexity setting enabled
- [Preview]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Preview]: Audit Windows VMs that do not have a minimum password age of 1 day
- [Preview]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Preview]: Audit Windows VMs that allow re-use of the previous 24 passwords

Tento podrobný plán také pomáhá řídit přístup k prostředkům Azure pomocí přiřazení sedm definic Azure Policy. Tyto zásady auditování typy prostředků a konfigurace, které vám umožní mnohem mírnější přístup k prostředkům. Principy prostředky, které jsou v rozporu se tyto zásady lze provést opravné akce k zajištění přístupu k prostředkům Azure nápovědy je omezené na autorizované uživatele.

- [Preview]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [Preview]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [Preview]: Audit Linux VMs that have accounts without passwords
- [Preview]: Audit Linux VMs that allow remote connections from accounts without passwords
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manageru
- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manageru
- Auditovat virtuální počítače, které nepoužívají spravované disky

## <a name="11-external-interface-protection"></a>11 externí rozhraní ochrany

Než pomocí více než 25 zásady správy zabezpečení odpovídající uživatelů, Tento podrobný plán vám pomůže chránit rozhraní služby před neoprávněným přístupem pomocí přiřazení [Azure Policy](../../../policy/overview.md) definice, která monitoruje neomezený účty úložiště. Účty úložiště s neomezeným přístupem můžete povolit nežádoucí přístup informace obsažené v informačním systému. Tento podrobný plán také přiřadí zásadu, která umožňuje adaptivní řízení aplikací na virtuálních počítačích.

- Audit neomezený přístup k síti do účtů úložiště
- Na virtuálních počítačích musí být aktivována adaptivní řízení aplikací

## <a name="12-secure-service-administration"></a>Správa 12 zabezpečení služby

Azure implementuje na základě rolí řízení přístupu (RBAC) k pomáhá, která spravujete, kdo má přístup k prostředkům v Azure. Pomocí webu Azure portal, můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán vám pomůže omezit a řídit práva privilegovaného přístupu přiřazením pět [Azure Policy](../../../policy/overview.md) definice a auditovat externí účty s vlastníkem a/nebo zápis oprávnění a účty s vlastníkem a/nebo oprávnění k zápisu které nemají povolené vícefaktorové ověřování.

Systémy pro správu cloudové služby se vysoce privilegovaný přístup k této službě. Jejich ohrožení zabezpečení by mít významný dopad, včetně způsob, jak obejít kontrolní mechanismy zabezpečení a ukrást nebo zpracování velkých objemů dat. Metody používané poskytovatele služeb správce spravovat operační službu by se měly navrhovat pro zmírnění rizika využívání, které by mohly ohrozit zabezpečení služby. Pokud není implementována tato zásada, útočník může být způsob, jak obejít kontrolní mechanismy zabezpečení a ukrást nebo zpracování velkých objemů dat.

- MFA povolena na účty s oprávněními vlastníka v rámci předplatného
- Vícefaktorové ověřování musí být povolené účty s oprávněním pro zápis v předplatném
- By měla být z předplatného odebrat externí účty s oprávněními vlastníka
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis

Tento podrobný plán přiřadí dvě definice zásad Azure a kontrolovat použití ověřování pomocí Azure Active Directory pro SQL servery a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft.

- By mělo proběhnout zřízení správce Azure Active Directory pro servery SQL
- Clustery Service Fabric měli používat jenom služby Azure Active Directory pro ověřování klientů

Tento podrobný plán také přiřadí čtyři definic Azure Policy auditovat účty, které by měl být nastaveno jako prioritní ke kontrole, včetně odepsanou účtů a externí účty se zvýšenými oprávněními. Pokud je nepotřebujete, účtů může blokovat přihlášení (nebo odstranit), která okamžitě Odebere přístupová práva k prostředkům Azure. Tento podrobný plán dvěma definicemi zásad Azure přiřadí auditu odpisy účet, který by měl být pro odebrání.

- By měla být z předplatného odebrat zastaralé účty
- By měla být z předplatného odebrat zastaralé účty s oprávněními vlastníka
- By měla být z předplatného odebrat externí účty s oprávněními vlastníka
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis

Tento podrobný plán také přiřadí definici rozhraní Azure Policy, kdy se auditují virtuální počítač s Linuxem heslo souboru oprávnění pro výstrahy v případě, že máte správně nastavené. Tento návrh umožňuje provádět nápravné akce, která Ujistěte se, že nebudou ohroženy ověřovací data.

- [Preview]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>Informace o 13 auditování pro uživatele

Tento podrobný plán vám pomůže zajistit systémové události jsou protokolovány přiřazením 6 [Azure Policy](../../../policy/overview.md) definice, které auditování nastavení protokolu pro prostředky Azure. Přiřazených zásad se taky Audituje, jestli virtuální počítače nejsou posílá protokoly do pracovního prostoru zadané log analytics.

- Monitorovat neauditovanou SQL serverech v Azure Security Center
- Auditování nastavení diagnostiky
- Auditování nastavení úrovně auditování SQL serveru.
- [Preview]: Deploy Log Analytics Agent for Linux VMs
- [Preview]: Deploy Log Analytics Agent for Windows VMs
- Nasazení network watcheru při vytvoření virtuálních sítí

## <a name="next-steps"></a>Další postup

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
