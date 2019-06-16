---
title: Zabezpečení Azure Key Vault | Dokumentace Microsoftu
description: Spravujte přístupová oprávnění pro Azure Key Vault, klíče a tajné kódy. Popisuje model ověřování a autorizace pro Key Vault a postupy zabezpečení trezoru klíčů.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 5b32e4897e718e0e411caf9ba76b036f1352bde0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715281"
---
# <a name="azure-key-vault-security"></a>Zabezpečení Azure Key Vault

Potřebujete chránit šifrovací klíče a tajné kódy jako jsou certifikáty, připojovací řetězce a hesla v cloudu, aby se pomocí Azure Key Vault. Od té doby se ukládání citlivých a důležitými obchodními daty, které musíte provést kroky k maximalizaci zabezpečení svých trezorů a data jsou v nich uložené. Tento článek se zabývá některé koncepty, které byste měli zvážit při návrhu vašeho zabezpečení Azure Key Vault.

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Když vytvoříte trezor klíčů v předplatném Azure, automaticky se přidruží k tenantovi Azure AD předplatného. Všem uživatelům se pokoušíte spravovat, nebo načítat obsah z úložiště musejí být ověřeny službou Azure AD.

- Ověření určí identitu volajícího.
- Autorizace následně určí, které operace můžete provádět volající. Autorizace ve službě Key Vault používá kombinaci [řízení přístupu podle rolí](../role-based-access-control/overview.md) (RBAC) a zásady přístupu trezoru klíčů Azure.

### <a name="access-model-overview"></a>Přehled přístupu k modelu

Přístup k trezorům probíhá přes dvě rozhraní nebo plochy. Těmito rovinami jsou rovina správy a rovina dat.

- *Roviny správy* Správa služby Key Vault samotného a jedná se o rozhraní umožňuje vytvářet a odstraňovat trezory. Může také číst vlastnosti trezoru klíčů a spravovat zásady přístupu.
- *Rovina dat* umožňuje pracovat s daty uloženými ve službě key vault. Můžete přidat, odstranit a upravit klíče, tajné kódy a certifikáty.

Pro přístup k službě key vault v jedné rovině, musí být ověřený a autorizovaný všichni volající (uživatelé a aplikace). Obě roviny pomocí Azure Active Directory (Azure AD) pro ověřování. K autorizaci ale rovina správy používá řízení přístupu na základě role (RBAC) a rovina dat používá zásady přístupu trezoru klíčů.

Model jediného mechanismu ověřování pro obě roviny má několik výhod:

- Organizace můžou řídit přístup centrálně ke všem trezorům klíčů v organizaci.
- Pokud uživatel opustí, ztratí okamžitě přístup ke všem trezorům klíčů v organizaci.
- Organizace si mohou ověření přizpůsobit pomocí možností v Azure AD, například k povolení služby Multi-Factor authentication pro zvýšení zabezpečení

### <a name="managing-administrative-access-to-key-vault"></a>Správa přístup pro správu do služby Key Vault

Když vytvoříte trezor klíčů ve skupině prostředků, spravujte přístup pomocí služby Azure AD. Uživatelům nebo skupinám schopnost spravovat trezory klíčů ve skupině prostředků. Můžete udělit přístup na úrovni konkrétním oboru přiřazením odpovídajících rolí RBAC. K udělení přístupu uživatelům ke správě trezorů klíčů, přiřadíte i předdefinovanou `key vault Contributor` role pro uživatele v konkrétním oboru. Následující úrovně obory je možné přiřadit k roli RBAC:

- **Předplatné**: Roli RBAC přiřazená na úrovni předplatného se vztahuje na všechny skupiny prostředků a prostředky v tomto předplatném.
- **Skupina prostředků**: Roli RBAC přiřazené na úrovni skupiny prostředků se vztahuje na všechny prostředky v příslušné skupině prostředků.
- **Konkrétní prostředek**: Roli RBAC přiřazené pro určitý prostředek se vztahuje na tento prostředek. Prostředek je v tomto případě určitý trezor klíčů.

Existuje několik předdefinovaných rolí. Pokud předdefinované role nevejde vašim potřebám, můžete definovat vlastní role. Další informace najdete v tématu [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Pokud má uživatel `Contributor` oprávnění k rovině správy trezoru klíčů, uživatel může udělit přístup k rovině dat pomocí nastavení zásad přístupu trezoru klíčů. By měl důsledně řídit, kdo má `Contributor` přístup role k vašim trezorům klíčů. Ujistěte se, že jen autorizované osoby můžete přístup k a spravovat trezory klíčů, klíče, tajné kódy a certifikáty.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Řízení přístupu k datům služby Key Vault

Zásady přístupu trezoru klíčů udělují oprávnění samostatně pro klíče, tajné klíče a certifikátu. Můžete udělit přístup uživatelů jenom na klíče a tajné kódy. Přístupová oprávnění pro klíče, tajné kódy a certifikáty se spravují na úrovni trezoru.

> [!IMPORTANT]
> Zásady přístupu trezoru klíčů nepodporují podrobné, na úrovni objektů oprávnění jako konkrétní klíč, tajný klíč nebo certifikát. Když uživatel jsou udělena oprávnění vytvářet a odstraňovat klíče, mohou provádět tyto operace na všech klíčích v daném trezoru klíčů.

Chcete-li nastavit zásady přístupu pro trezor klíčů, použijte [webu Azure portal](https://portal.azure.com/), [rozhraní příkazového řádku Azure](../cli-install-nodejs.md), [prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs), nebo [Key Vault Management REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Můžete omezit přístup k rovině dat pomocí [koncové body služeb virtuální sítě pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Můžete nakonfigurovat [brány firewall a pravidla virtuální sítě](key-vault-network-security.md) pro další vrstvu zabezpečení.

## <a name="network-access"></a>Síťový přístup

Můžete snížit vystavení vašim trezorům tak, že určíte, které IP adresy mají přístup k nim. Koncové body služeb virtuální sítě pro Azure Key Vault umožňují omezit přístup k zadané virtuální sítě. Koncové body umožňují omezit přístup k seznamu rozsahů adres IPv4 (internet protocol verze 4). Každý uživatel, připojení k vašemu trezoru klíčů z mimo tyto zdroje je odepřen přístup.

Po Brány firewall pravidla jsou aktivní, uživatelé mohou číst pouze data ze služby Key Vault při jejich požadavky pocházejí z povolených virtuálních sítí nebo rozsahy adres IPv4. To platí i pro přístup k trezoru klíčů z portálu Azure portal. I když můžou uživatelé procházet k trezoru klíčů z portálu Azure portal, se nebudou moct vypsat klíče, tajné kódy nebo certifikáty Pokud klientském počítači se nenachází v seznamu povolených aplikací. To ovlivňuje také výběr Key Vault pomocí dalších služeb Azure. Uživatelé možná půjde zobrazit seznam trezorů klíčů, ale v seznamu klíčů, je-li zabránit pravidla brány firewall na klientském počítači.

Další informace o službě Azure Key Vault síťová adresa revize [koncové body služeb virtuální sítě pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitorování

Protokolování v Key Vault uloží informace o aktivity prováděné u trezoru. Protokoly Key Vault:

- Všechny ověřené požadavky REST API, včetně neúspěšných žádostí
  - Operace s klíči samotným trezorem. Tyto operace patří vytvoření, odstranění, nastavení zásad přístupu a aktualizaci atributů trezoru klíčů, jako jsou například značky.
  - Operace nad klíči a tajnými kódy ve službě key vault, včetně:
    - Vytváření, úprava nebo odstraňování těchto klíčů nebo tajných kódů.
    - Podepsání, ověření, šifrování, dešifrování, zabalení a rozbalení klíčů, získání tajné klíče, výpis klíčů a tajných kódů (a jejich verze).
- Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Příklady jsou požadavky, které nemají nosný token, které jsou poškozené nebo jejichž platnost vypršela, nebo mají neplatný token.

Informace o protokolování jsou přístupné během deseti minut po provedení této operace služby key vault. To je na vás Správa protokolů v účtu úložiště. 

- Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
- Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.

Doporučení týkající se bezpečně správy úložiště účtů najdete v tématu [Průvodci zabezpečením Azure Storage](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>Další kroky

- [Koncové body služeb virtuální sítě pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md)
- [Koncové body služeb virtuální sítě pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
