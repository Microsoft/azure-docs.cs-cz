---
title: Zabezpečení azure key vault | Dokumenty společnosti Microsoft
description: Spravujte přístupová oprávnění pro Azure Key Vault, klíče a tajné klíče. Zahrnuje model ověřování a autorizace pro trezor klíčů a způsob zabezpečení trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 3cc5cb68f430ac8e5070b9c8c4a1aa0c28639311
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270898"
---
# <a name="azure-key-vault-security"></a>Zabezpečení azure trezoru klíčů

Musíte chránit šifrovací klíče a tajné klíče, jako jsou certifikáty, připojovací řetězce a hesla v cloudu, takže používáte Azure Key Vault. Vzhledem k tomu, že ukládáte citlivá a důležitá obchodní data, je třeba podniknout kroky k maximalizaci zabezpečení trezorů a dat v nich uložených. Tento článek se bude týkat některé koncepty, které byste měli zvážit při navrhování zabezpečení Azure Key Vault.

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Když vytvoříte trezor klíčů v předplatném Azure, automaticky se přidruží k tenantovi Azure AD předplatného. Každý, kdo se pokouší spravovat nebo načítat obsah z úložiště, musí být ověřen službou Azure AD.

- Ověřování stanoví identitu volajícího.
- Autorizace určuje, které operace může volající provádět. Autorizace v trezoru klíčů používá kombinaci [řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC) a zásad přístupu azure key vault.

### <a name="access-model-overview"></a>Přehled modelu aplikace Access

Přístup k trezorům probíhá prostřednictvím dvou rozhraní nebo rovin. Tato letadla jsou řídící rovinou a datovou rovinou.

- *Rovina správy* je místo, kde můžete spravovat trezor klíčů sám a je rozhraní slouží k vytvoření a odstranění trezorů. Můžete také číst vlastnosti trezoru klíčů a spravovat zásady přístupu.
- *Rovina dat* umožňuje pracovat s daty uloženými v trezoru klíčů. Klíče, tajné klíče a certifikáty můžete přidávat, odstraňovat a upravovat.

Chcete-li získat přístup k trezoru klíčů v jedné rovině, musí být všichni volající (uživatelé nebo aplikace) ověřeni a autorizováni. Obě letadla používají Azure Active Directory (Azure AD) pro ověřování. Pro autorizaci rovina správy používá řízení přístupu na základě rolí (RBAC) a rovina dat používá zásady přístupu trezoru klíčů.

Model jednoho mechanismu pro ověřování obou rovin má několik výhod:

- Organizace mohou centrálně řídit přístup ke všem trezorům klíčů ve své organizaci.
- Pokud uživatel odejde, okamžitě ztratí přístup ke všem trezorům klíčů v organizaci.
- Organizace můžou přizpůsobit ověřování pomocí možností ve službě Azure AD, například povolit vícefaktorové ověřování pro zvýšení zabezpečení.

### <a name="managing-administrative-access-to-key-vault"></a>Správa přístupu správce k trezoru klíčů

Když vytvoříte trezor klíčů ve skupině prostředků, spravujete přístup pomocí Azure AD. Uživatelům nebo skupinám udělujete možnost spravovat trezory klíčů ve skupině prostředků. Přístup na určité úrovni oboru můžete udělit přiřazením příslušných rolí RBAC. Chcete-li uživateli udělit přístup ke správě `key vault Contributor` trezorů klíčů, přiřadíte uživateli předdefinovanou roli v určitém oboru. Roli RBAC lze přiřadit následující úrovně oborů:

- **Předplatné**: Role RBAC přiřazená na úrovni předplatného platí pro všechny skupiny prostředků a prostředky v rámci tohoto předplatného.
- **Skupina prostředků**: Role RBAC přiřazená na úrovni skupiny prostředků se vztahuje na všechny prostředky v této skupině prostředků.
- **Konkrétní zdroj**: Pro daný prostředek se vztahuje role RBAC přiřazená pro určitý zdroj. V tomto případě je prostředek konkrétní trezor klíčů.

Existuje několik předdefinovaných rolí. Pokud předdefinovaná role nevyhovuje vašim potřebám, můžete definovat vlastní roli. Další informace naleznete v tématu [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Pokud má `Contributor` uživatel oprávnění k rovině správy trezoru klíčů, může si udělit přístup k rovině dat nastavením zásad přístupu trezoru klíčů. Měli byste přísně řídit, kdo má `Contributor` přístup k vašim trezorům klíčů. Zajistěte, aby k vašim trezorům klíčů, klíčům, tajným klíčům a certifikátům měly přístup a spravovat je pouze oprávněné osoby.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Řízení přístupu k datům trezoru klíčů

Zásady přístupu trezoru klíčů udělují oprávnění samostatně klíčům, tajným klíčům nebo certifikátům. Uživateli můžete udělit přístup pouze ke klíčům, nikoli k tajným kódům. Přístupová oprávnění pro klíče, tajné klíče a certifikáty jsou spravována na úrovni úložiště.

> [!IMPORTANT]
> Zásady přístupu trezoru klíčů nepodporují podrobná oprávnění na úrovni objektů, jako je konkrétní klíč, tajný klíč nebo certifikát. Pokud je uživateli uděleno oprávnění k vytváření a odstraňování klíčů, může tyto operace provádět se všemi klíči v tomto trezoru klíčů.

Pokud chcete nastavit zásady přístupu pro trezor klíčů, použijte [portál Azure](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)nebo [REST API správy trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Přístup k rovině dat můžete omezit pomocí [koncových bodů služby virtuální sítě pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). [Brány firewall a pravidla virtuální sítě](key-vault-network-security.md) můžete nakonfigurovat pro další vrstvu zabezpečení.

## <a name="network-access"></a>Síťový přístup

Expozici trezorů můžete snížit určením adres IP, které k nim mají přístup. Koncové body služby virtuální sítě pro Azure Key Vault umožňují omezit přístup k zadané virtuální síti. Koncové body také umožňují omezit přístup k seznamu rozsahů adres IPv4 (internetový protokol verze 4). Všem uživatelům, kteří se připojují k trezoru klíčů mimo tyto zdroje, je odepřen přístup.

Po použití pravidel brány firewall mohou uživatelé číst data z trezoru klíčů pouze v případě, že jejich požadavky pocházejí z povolených virtuálních sítí nebo rozsahů adres IPv4. To platí také pro přístup k trezoru klíčů z webu Azure Portal. Přestože uživatelé mohou přejít do trezoru klíčů z portálu Azure, nemusí být schopni seznam klíčů, tajných kódů nebo certifikátů, pokud jejich klientský počítač není v seznamu povolených. To má také vliv na výběr trezoru klíčů jinými službami Azure. Uživatelé mohou zobrazit seznam trezorů klíčů, ale ne klíče seznamu, pokud pravidla brány firewall brání jejich klientském počítači.

Další informace o síťové adrese Azure Key Vault zkontrolujte [koncové body služby Virtuální síť pro azure key vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitorování

Protokolování trezoru klíčů ukládá informace o aktivitách prováděných v trezoru. Protokoly trezoru klíčů:

- Všechny ověřené požadavky rozhraní REST API, včetně neúspěšných požadavků
  - Operace samotného trezoru klíčů. Tyto operace zahrnují vytváření, odstraňování, nastavení zásad přístupu a aktualizaci atributů trezoru klíčů, jako jsou značky.
  - Operace s klíči a tajnými klíči v trezoru klíčů, včetně:
    - Vytváření, úpravy nebo odstranění těchto klíčů nebo tajných klíčů.
    - Podepisování, ověřování, šifrování, dešifrování, balení a rozbalení klíčů, získávání tajných kódů a výpis klíčů a tajných kódů (a jejich verzí).
- Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Příklady jsou požadavky, které nemají nosný token, které jsou poškozené nebo vypršela nebo které mají neplatný token.

Informace o protokolování jsou přístupné do 10 minut po operaci trezoru klíčů. Je na vás, abyste mohli spravovat své protokoly v účtu úložiště.

- Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
- Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.

Doporučení ohledně bezpečné správy účtů úložiště najděte [průvodce zabezpečením úložiště Azure.](../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Další kroky

- [Koncové body služby Virtuální síť pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md)
- [Koncové body služby virtuální sítě pro azure key vault](key-vault-overview-vnet-service-endpoints.md)
