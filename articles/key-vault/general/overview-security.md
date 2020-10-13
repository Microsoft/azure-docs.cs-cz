---
title: Zabezpečení služby Azure Key Vault
description: Spravujte přístupová oprávnění pro Azure Key Vault, klíče a tajné kódy. Popisuje model ověřování a autorizace Key Vault a způsob zabezpečení trezoru klíčů.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: c3dd4e5138741a3c035507358830f3572cf92751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739686"
---
# <a name="azure-key-vault-security"></a>Zabezpečení služby Azure Key Vault

Azure Key Vault slouží k ochraně šifrovacích klíčů a tajných kódů, jako jsou certifikáty, připojovací řetězce a hesla v cloudu. Při ukládání citlivých a důležitých podnikových dat je nutné provést kroky k maximalizaci zabezpečení trezorů a dat, která jsou v nich uložená.

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Když vytvoříte Trezor klíčů v rámci předplatného Azure, automaticky se přiřadí k tenantovi Azure AD daného předplatného. Každý, kdo se snaží spravovat nebo načítat obsah z trezoru, se musí ověřit pomocí Azure AD.

- Ověřování vytváří identitu volajícího.
- Autorizace určuje, které operace může volající provést. Autorizace v Key Vault používá kombinaci [řízení přístupu na základě role](../../role-based-access-control/overview.md) (RBAC) a zásady přístupu Azure Key Vault.

### <a name="access-model-overview"></a>Přehled modelu přístupu

Přístup k trezorům probíhá přes dvě rozhraní nebo roviny. Tyto roviny představují rovinu správy a rovinu dat.

- *Rovina správy* je místo, kde spravujete Key Vault sebe sama a jedná se o rozhraní používané k vytváření a odstraňování trezorů. Můžete také číst vlastnosti trezoru klíčů a spravovat zásady přístupu.
- *Rovina dat* umožňuje pracovat s daty uloženými v trezoru klíčů. Můžete přidávat, odstraňovat a upravovat klíče, tajné klíče a certifikáty.

Pro přístup k trezoru klíčů v kterékoliv rovině musí být všichni volající (uživatelé a aplikace) ověřeni a autorizováni. Obě roviny používají pro ověřování Azure Active Directory (Azure AD). Pro autorizaci rovina správy používá řízení přístupu na základě role (RBAC) a rovina dat používá zásady přístupu Key Vault.

Model jednoho mechanismu ověřování do obou rovin má několik výhod:

- Organizace můžou centrálně řídit přístup ke všem trezorům klíčů ve své organizaci.
- Pokud uživatel opustí, okamžitě ztratí přístup ke všem trezorům klíčů v organizaci.
- Organizace můžou přizpůsobit ověřování pomocí možností ve službě Azure AD, jako je například povolení služby Multi-Factor Authentication pro zvýšení zabezpečení.

### <a name="managing-administrative-access-to-key-vault"></a>Správa přístupu pro správu k Key Vault

Při vytváření trezoru klíčů ve skupině prostředků můžete spravovat přístup pomocí Azure AD. Uživatelům nebo skupinám udělíte možnost spravovat trezory klíčů ve skupině prostředků. Přiřazením příslušných rolí Azure můžete udělit přístup na konkrétní úrovni oboru. Chcete-li uživateli udělit přístup ke správě trezorů klíčů, přiřaďte uživatele předdefinované `key vault Contributor` role v konkrétním oboru. K roli Azure se dají přiřadit tyto úrovně oborů:

- **Předplatné**: role Azure přiřazená na úrovni předplatného se vztahuje na všechny skupiny prostředků a prostředky v rámci daného předplatného.
- **Skupina prostředků**: role Azure přiřazená na úrovni skupiny prostředků se vztahuje na všechny prostředky v této skupině prostředků.
- **Konkrétní prostředek**: na tento prostředek se vztahuje role Azure přiřazená pro konkrétní prostředek. V tomto případě je prostředkem konkrétní Trezor klíčů.

Existuje několik předdefinovaných rolí. Pokud předdefinovaná role nevyhovuje vašim potřebám, můžete definovat vlastní roli. Další informace naleznete v části [RBAC: předdefinované role](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Pokud má uživatel `Contributor` oprávnění k rovině správy trezoru klíčů, uživatel může udělit přístup k rovině dat nastavením zásad přístupu Key Vault. Měli byste přesně řídit, kdo má `Contributor` roli přístup k vašim trezorům klíčů. Ujistěte se, že k vašim trezorům klíčů, klíčům, tajným klíčům a certifikátům mají přístup jenom autorizovaní uživatelé.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Řízení přístupu k datům Key Vault

Zásady přístupu Key Vault udělují oprávnění nezávisle na klíčích, tajných klíčích a certifikátech. Uživatelům můžete udělit přístup jenom ke klíčům a nikoli k tajným klíčům. Přístupová oprávnění pro klíče, tajné klíče a certifikáty se spravují na úrovni trezoru.

> [!IMPORTANT]
> Zásady přístupu Key Vault nepodporují podrobné oprávnění na úrovni objektu, jako je konkrétní klíč, tajný klíč nebo certifikát. Když je uživateli udělené oprávnění k vytváření a odstraňování klíčů, můžou tyto operace provádět u všech klíčů v tomto trezoru klíčů.

Zásady přístupu pro Trezor klíčů můžete nastavit pomocí [Azure Portal](assign-access-policy-portal.md), rozhraní příkazového [řádku Azure CLI](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)nebo [rozhraní REST API pro správu Key Vault](/rest/api/keyvault/).

Přístup k rovině dat můžete omezit pomocí [koncových bodů služby virtuální sítě pro Azure Key Vault](overview-vnet-service-endpoints.md)). Můžete nakonfigurovat [brány firewall a pravidla virtuální sítě](network-security.md) pro další vrstvu zabezpečení.

## <a name="network-access"></a>Síťový přístup

Chcete-li snížit riziko vašich trezorů, určete, které IP adresy k nim mají přístup. Koncové body služby virtuální sítě pro Azure Key Vault umožňují omezit přístup k zadané virtuální síti. Koncové body také umožňují omezit přístup k seznamu rozsahů adres protokolu IPv4 (Internet Protocol verze 4). Přístup všem uživatelům, kteří se připojují k trezoru klíčů mimo tyto zdroje, má odepřený přístup.

Po uplatnění pravidel brány firewall můžou uživatelé číst data z Key Vault jenom v případě, že jejich požadavky pocházejí z povolených virtuálních sítí nebo rozsahů IPv4 adres. To platí také pro přístup k Key Vault z Azure Portal. I když uživatelé můžou přejít k trezoru klíčů z Azure Portal, nemusí být schopni zobrazit seznam klíčů, tajných kódů ani certifikátů, pokud jejich klientský počítač není v seznamu povolených. To má vliv také na Key Vault pro výběr jinými službami Azure. Uživatelé můžou zobrazit seznam trezorů klíčů, ale ne seznam klíčů, pokud pravidla brány firewall brání jejich klientskému počítači.

Další informace o Azure Key Vaultch [koncových bodech služby virtuální sítě pro Azure Key Vault](overview-vnet-service-endpoints.md)) najdete v ch síťových adresách.

## <a name="tls-and-https"></a>TLS a HTTPS

*   Key Vault front-endu (rovina dat) je server s více klienty. To znamená, že trezory klíčů od různých zákazníků můžou sdílet stejnou veřejnou IP adresu. Aby bylo možné dosáhnout izolace, jednotlivé požadavky HTTP jsou ověřeny a autorizovány nezávisle na ostatních požadavcích.
*   Můžete identifikovat starší verze protokolu TLS, aby se nahlásily chyby zabezpečení, ale protože je sdílená veřejná IP adresa, není možné, aby tým služby trezoru klíčů zakázal starší verze TLS pro jednotlivé trezory klíčů na úrovni přenosu.
*   Protokol HTTPS umožňuje klientovi účast v vyjednávání TLS. **Klienti můžou vyhovět nejnovější verzi TLS**a vždycky, když to klient provede, bude celé připojení používat odpovídající ochranu úrovně. Skutečnost, že Key Vault stále podporuje starší verze TLS, nezhoršuje zabezpečení připojení pomocí novější verze TLS.
*   Navzdory známým chybám zabezpečení v protokolu TLS neexistuje žádný známý útok, který by mohl škodlivému agentovi extrahovat jakékoli informace z vašeho trezoru klíčů, když útočník iniciuje spojení s verzí TLS s chybami zabezpečení. Útočník by stále musel ověřovat a autorizovat sebe sama a pokud se legitimní klienti vždy připojují k nejnovějším verzím TLS, neexistuje žádný způsob, jak by přihlašovací údaje mohly být v původních verzích TLS neúniky z chyb zabezpečení.

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Protokolování Key Vault ukládá informace o aktivitách provedených ve vašem trezoru. Úplné podrobnosti najdete v tématu [protokolování Key Vault](logging.md).

Doporučení pro zabezpečenou správu účtů úložiště najdete v [Průvodci zabezpečením Azure Storage](../../storage/blobs/security-recommendations.md) .

## <a name="next-steps"></a>Další kroky

- [Koncové body služby virtuální sítě pro Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC: předdefinované role](../../role-based-access-control/built-in-roles.md)