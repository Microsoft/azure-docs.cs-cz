---
title: Přehled zabezpečení Azure Key Vault
description: Přehled funkcí zabezpečení a osvědčených postupů pro Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: c7635fdc2012ab404709733d8f5849465c2ee82f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071559"
---
# <a name="azure-key-vault-security"></a>Zabezpečení služby Azure Key Vault

Azure Key Vault slouží k ochraně šifrovacích klíčů a tajných kódů, jako jsou certifikáty, připojovací řetězce a hesla v cloudu. Při ukládání citlivých a důležitých podnikových dat je nutné provést kroky k maximalizaci zabezpečení trezorů a dat, která jsou v nich uložená.

Tento článek obsahuje přehled funkcí zabezpečení a osvědčených postupů pro Azure Key Vault. 

> [!NOTE]
> Úplný seznam Azure Key Vaultch doporučení zabezpečení najdete v tématu základní informace o [zabezpečení pro Azure Key Vault](security-baseline.md).

## <a name="network-security"></a>Zabezpečení sítě

Chcete-li snížit riziko vašich trezorů, určete, které IP adresy k nim mají přístup. Koncové body služby virtuální sítě pro Azure Key Vault umožňují omezit přístup k zadané virtuální síti. Koncové body také umožňují omezit přístup k seznamu rozsahů adres protokolu IPv4 (Internet Protocol verze 4). Přístup všem uživatelům, kteří se připojují k trezoru klíčů mimo tyto zdroje, má odepřený přístup.  Úplné podrobnosti najdete v tématu [koncové body služby virtuální sítě pro Azure Key Vault](overview-vnet-service-endpoints.md) .

Po uplatnění pravidel brány firewall můžou uživatelé číst data z Key Vault jenom v případě, že jejich požadavky pocházejí z povolených virtuálních sítí nebo rozsahů IPv4 adres. To platí také pro přístup k Key Vault z Azure Portal. I když uživatelé můžou přejít k trezoru klíčů z Azure Portal, nemusí být schopni zobrazit seznam klíčů, tajných kódů ani certifikátů, pokud jejich klientský počítač není v seznamu povolených. To má vliv také na Key Vault pro výběr jinými službami Azure. Uživatelé můžou zobrazit seznam trezorů klíčů, ale ne seznam klíčů, pokud pravidla brány firewall brání jejich klientskému počítači.  Postup implementace najdete v tématu [konfigurace Azure Key Vault bran firewall a virtuálních sítí](network-security.md) .

Služba privátního propojení Azure umožňuje přístup k Azure Key Vault a hostovaným zákaznickým a partnerským službám Azure přes privátní koncový bod ve vaší virtuální síti. Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.  Postup implementace najdete v tématu věnovaném [integraci Key Vault s privátním propojením Azure](private-link-service.md) .

## <a name="tls-and-https"></a>TLS a HTTPS

- Key Vault front-endu (rovina dat) je server s více klienty. To znamená, že trezory klíčů od různých zákazníků můžou sdílet stejnou veřejnou IP adresu. Aby bylo možné dosáhnout izolace, jednotlivé požadavky HTTP jsou ověřeny a autorizovány nezávisle na ostatních požadavcích.
- Můžete identifikovat starší verze protokolu TLS, aby se nahlásily chyby zabezpečení, ale protože je sdílená veřejná IP adresa, není možné, aby tým služby trezoru klíčů zakázal starší verze TLS pro jednotlivé trezory klíčů na úrovni přenosu.
- Protokol HTTPS umožňuje klientovi účast v vyjednávání TLS. **Klienti můžou vyhovět nejnovější verzi TLS** a vždycky, když to klient provede, bude celé připojení používat odpovídající ochranu úrovně. Skutečnost, že Key Vault stále podporuje starší verze TLS, nezhoršuje zabezpečení připojení pomocí novější verze TLS.
- Navzdory známým chybám zabezpečení v protokolu TLS neexistuje žádný známý útok, který by mohl škodlivému agentovi extrahovat jakékoli informace z vašeho trezoru klíčů, když útočník iniciuje spojení s verzí TLS s chybami zabezpečení. Útočník by stále musel ověřovat a autorizovat sebe sama a pokud se legitimní klienti vždy připojují k nejnovějším verzím TLS, neexistuje žádný způsob, jak by přihlašovací údaje mohly být v původních verzích TLS neúniky z chyb zabezpečení.

## <a name="identity-management"></a>Správa identit

Když vytvoříte Trezor klíčů v rámci předplatného Azure, automaticky se přiřadí k tenantovi Azure AD daného předplatného. Každý, kdo se snaží spravovat nebo načítat obsah z trezoru, se musí ověřit pomocí Azure AD. V obou případech můžou aplikace získat přístup k Key Vault třemi způsoby:

- **Pouze aplikace**: aplikace představuje instanční objekt nebo spravovanou identitu. Tato identita je nejběžnější scénář pro aplikace, které pravidelně potřebují přistupovat k certifikátům, klíčům nebo tajným klíčům z trezoru klíčů. Aby tento scénář fungoval, `objectId` musí být aplikace zadaná v zásadách přístupu a `applicationId` nesmí být zadána nebo musí být zadaná  `null` .
- **Pouze uživatel**: uživatel přistupuje k trezoru klíčů z jakékoli aplikace zaregistrované v tenantovi. Příklady tohoto typu přístupu zahrnují Azure PowerShell a Azure Portal. Aby tento scénář fungoval, `objectId` musí být uživatel uveden v zásadách přístupu a `applicationId` nesmí být zadán nebo musí být zadán  `null` .
- **Aplikace-plus – uživatel** (někdy označovaný jako _složená identita_): uživatel je vyžadován pro přístup k trezoru klíčů z konkrétní aplikace _a_ aplikace musí k zosobnění uživatele používat tok spouštěný jménem ověřování (OBO). Aby tento scénář fungoval, `applicationId` `objectId` musí být v zásadách přístupu zadány obě i. `applicationId`Identifikuje požadovanou aplikaci a `objectId` identifikuje uživatele. V současné době tato možnost není k dispozici pro rovinu dat Azure RBAC (Preview).

Ve všech typech přístupu se aplikace ověřuje pomocí Azure AD. Aplikace používá jakoukoli [podporovanou metodu ověřování](../../active-directory/develop/authentication-vs-authorization.md) založenou na typu aplikace. Aplikace získá token pro prostředek v rovině pro udělení přístupu. Prostředek je koncový bod v rovině pro správu nebo data na základě prostředí Azure. Aplikace použije token a pošle REST API žádost o Key Vault. Pokud se chcete dozvědět víc, Projděte si [celý tok ověřování](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Úplné podrobnosti najdete v tématu [Key Vault základy ověřování](authentication-fundamentals.md) .

## <a name="privileged-access"></a>Privilegovaný přístup

Autorizace určuje, které operace může volající provést. Autorizace v Key Vault používá kombinaci [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) a zásady přístupu Azure Key Vault.

Přístup k trezorům probíhá přes dvě rozhraní nebo roviny. Tyto roviny představují rovinu správy a rovinu dat.

- *Rovina správy* je místo, kde spravujete Key Vault sebe sama a jedná se o rozhraní používané k vytváření a odstraňování trezorů. Můžete také číst vlastnosti trezoru klíčů a spravovat zásady přístupu.
- *Rovina dat* umožňuje pracovat s daty uloženými v trezoru klíčů. Můžete přidávat, odstraňovat a upravovat klíče, tajné klíče a certifikáty.

Aplikace přistupují k rovinám prostřednictvím koncových bodů. Ovládací prvky přístupu pro tyto dvě roviny pracují nezávisle. Abyste aplikaci udělili přístup k používání klíčů v trezoru klíčů, udělíte přístup k rovině dat pomocí zásad přístupu Key Vault nebo Azure RBAC (Preview). Pokud chcete uživateli udělit oprávnění ke čtení Key Vault vlastností a značek, ale ne přístup k datům (klíčům, tajným klíčům nebo certifikátům), udělíte přístup k rovině správy pomocí Azure RBAC.

V následující tabulce jsou uvedeny koncové body pro řídicí a datové roviny.

| &nbsp;Rovina přístupu | Koncové body přístupu | Operace | &nbsp;Mechanismus řízení přístupu |
| --- | --- | --- | --- |
| Rovina správy | **Globální**<br> management.azure.com:443<br><br> **Azure Čína 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Státní správa USA Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> management.microsoftazure.de:443 | Vytváření, čtení, aktualizace a odstraňování trezorů klíčů<br><br>Nastavení zásad přístupu Key Vault<br><br>Nastavení značek Key Vault | Azure RBAC |
| Rovina dat | **Globální**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure Čína 21Vianet:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Státní správa USA Azure:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 | Klíče: šifrování, dešifrování, wrapKey, unwrapKey, podpis, ověření, získání, vypsání, vytvoření, aktualizace, import, odstranění, obnovení, zálohování, obnovení, vyprázdnění<br><br> Certifikáty: managecontacts, getissuer, listissuers, setissuers, deleteissuers, manageissuers, získat, seznam, vytvořit, importovat, aktualizovat, odstranit, obnovit, zálohovat, obnovit, vymazat<br><br>  Tajné kódy: získání, seznam, nastavení, odstranění, obnovení, zálohování, obnovení, vyprázdnění | Zásada přístupu Key Vault nebo Azure RBAC (Preview)|

### <a name="managing-administrative-access-to-key-vault"></a>Správa přístupu pro správu k Key Vault

Při vytváření trezoru klíčů ve skupině prostředků můžete spravovat přístup pomocí Azure AD. Uživatelům nebo skupinám udělíte možnost spravovat trezory klíčů ve skupině prostředků. Přiřazením příslušných rolí Azure můžete udělit přístup na konkrétní úrovni oboru. Chcete-li uživateli udělit přístup ke správě trezorů klíčů, přiřaďte uživatele předdefinované `key vault Contributor` role v konkrétním oboru. K roli Azure se dají přiřadit tyto úrovně oborů:

- **Předplatné**: role Azure přiřazená na úrovni předplatného se vztahuje na všechny skupiny prostředků a prostředky v rámci daného předplatného.
- **Skupina prostředků**: role Azure přiřazená na úrovni skupiny prostředků se vztahuje na všechny prostředky v této skupině prostředků.
- **Konkrétní prostředek**: na tento prostředek se vztahuje role Azure přiřazená pro konkrétní prostředek. V tomto případě je prostředkem konkrétní Trezor klíčů.

Existuje několik předdefinovaných rolí. Pokud předdefinovaná role nevyhovuje vašim potřebám, můžete definovat vlastní roli. Další informace najdete v tématu [Azure RBAC: předdefinované role](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Pokud má uživatel `Contributor` oprávnění k rovině správy trezoru klíčů, uživatel může udělit přístup k rovině dat nastavením zásad přístupu Key Vault. Měli byste přesně řídit, kdo má `Contributor` roli přístup k vašim trezorům klíčů. Ujistěte se, že k vašim trezorům klíčů, klíčům, tajným klíčům a certifikátům mají přístup jenom autorizovaní uživatelé.

### <a name="controlling-access-to-key-vault-data"></a>Řízení přístupu k datům Key Vault

Zásady přístupu Key Vault udělují oprávnění nezávisle na klíčích, tajných klíčích a certifikátech. Uživatelům můžete udělit přístup jenom ke klíčům a nikoli k tajným klíčům. Přístupová oprávnění pro klíče, tajné klíče a certifikáty se spravují na úrovni trezoru.

> [!IMPORTANT]
> Zásady přístupu Key Vault nepodporují podrobné oprávnění na úrovni objektu, jako je konkrétní klíč, tajný klíč nebo certifikát. Když je uživateli udělené oprávnění k vytváření a odstraňování klíčů, můžou tyto operace provádět u všech klíčů v tomto trezoru klíčů.

Zásady přístupu pro Trezor klíčů můžete nastavit pomocí [Azure Portal](assign-access-policy-portal.md), rozhraní příkazového [řádku Azure CLI](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)nebo [rozhraní REST API pro správu Key Vault](/rest/api/keyvault/).

Přístup k rovině dat můžete omezit pomocí [koncových bodů služby virtuální sítě pro Azure Key Vault](overview-vnet-service-endpoints.md)). Můžete nakonfigurovat [brány firewall a pravidla virtuální sítě](network-security.md) pro další vrstvu zabezpečení.

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Protokolování Key Vault ukládá informace o aktivitách provedených ve vašem trezoru. Úplné podrobnosti najdete v tématu [protokolování Key Vault](logging.md).

Můžete integrovat Key Vault s Event Grid, abyste byli informováni o tom, že se změnil stav klíče, certifikátu nebo tajného klíče uloženého v trezoru klíčů. Podrobnosti najdete v tématu [monitorování Key Vault s](event-grid-overview.md) využitím Azure Event Grid

Je také důležité monitorovat stav vašeho trezoru klíčů, abyste se ujistili, že vaše služba funguje tak, jak má. Další informace o tom, jak to udělat, najdete v tématu [monitorování a upozorňování na Azure Key Vault](alert.md).

## <a name="backup-and-recovery"></a>Backup a obnovení

Ochrana pomocí Azure Key Vaultho obnovitelného odstranění a vymazání umožňuje obnovit odstraněné trezory a objekty trezoru. Úplné podrobnosti najdete v tématu [přehled Azure Key Vaultho obnovitelného odstranění](soft-delete-overview.md).

Měli byste také při aktualizaci, odstranění nebo vytváření objektů v rámci trezoru provést pravidelnou zálohu vašeho trezoru.  

## <a name="next-steps"></a>Další kroky

- [Základní Azure Key Vault zabezpečení](security-baseline.md)
- [Azure Key Vault osvědčené postupy](security-baseline.md)
- [Koncové body služby Azure Key Vault pro virtuální síť](overview-vnet-service-endpoints.md)
- [Azure RBAC: předdefinované role](../../role-based-access-control/built-in-roles.md)
