---
title: Řízení přístupu ke spravovanému modulu HSM Azure
description: Spravujte přístupová oprávnění pro spravovaný modul HSM a klíče Azure. Popisuje model ověřování a autorizace pro spravovaný modul HSM a způsob zabezpečení HSM.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 803dc4d1a7b78df891780eb741cba4e57ab2d5dc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92784418"
---
# <a name="managed-hsm-access-control"></a>Řízení přístupu pro Managed HSM

> [!NOTE]
> Poskytovatel prostředků Key Vault podporuje dva typy prostředků: **trezory** a **spravované HSM** . Řízení přístupu popsané v tomto článku platí jenom pro **spravované HSM** . Další informace o řízení přístupu pro spravovaný modul HSM najdete v tématu [poskytnutí přístupu k Key Vault klíčům, certifikátům a tajným klíčům pomocí řízení přístupu na základě role Azure](../general/rbac-guide.md).

Azure Key Vault spravovaný modul HSM je cloudová služba, která chrání šifrovací klíče. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, je potřeba zabezpečit přístup ke spravovaným HSM tím, že povolíte přístup jenom autorizovaným aplikacím a uživatelům. Tento článek poskytuje přehled spravovaného modelu řízení přístupu HSM. Vysvětluje ověřování a autorizaci a popisuje, jak zabezpečit přístup ke spravovaným HSM.

## <a name="access-control-model"></a>Model řízení přístupu

Přístup ke spravovanému modulu HARDWAROVÉho zabezpečení se ovládá prostřednictvím dvou rozhraní: **rovina správy** a **rovina dat** . Rovina správy je místo, kde spravujete samotný modul HSM. Mezi operace v této rovině patří vytváření a odstraňování spravovaných HSM a načítání spravovaných vlastností HSM. Rovina dat je místo, kde pracujete s daty uloženými ve spravovaném modulu HSM, který je šifrovacími klíči zálohovanými pomocí HSM. Můžete přidávat, odstraňovat, upravovat a používat klíče k provádění kryptografických operací, spravovat přiřazování rolí pro řízení přístupu k klíčům, vytvářet úplné zálohy HSM, obnovovat úplné zálohování a spravovat doménu zabezpečení z rozhraní roviny dat.

Pro přístup ke spravovanému modulu HSM v kterékoliv rovině musí mít všichni volající správné ověřování a autorizaci. Ověřování vytváří identitu volajícího. Autorizace určuje, které operace může volající spustit. Volající může být libovolný [objekt zabezpečení](../../role-based-access-control/overview.md#security-principal) definovaný v Azure Active Directory-User, Group, instanční objekt nebo spravovaná identita.

Obě roviny používají Azure Active Directory pro ověřování. Pro autorizaci používají různé systémy následujícím způsobem.
- Rovina správy používá řízení přístupu na základě role Azure – Azure RBAC – autorizační systém založený na Azure Azure Resource Manager 
- Rovina dat používá spravovanou RBAC na úrovni HSM (spravovaná místní RBAC) – autorizační systém implementovaný a vynucované na spravované úrovni HSM.

Když se vytvoří spravovaný modul HSM, žadatel taky nabídne seznam správců datové roviny (podporují se všechny [objekty zabezpečení](../../role-based-access-control/overview.md#security-principal) ). Pouze tito správci mají přístup ke spravované rovině dat HSM, aby mohli provádět klíčové operace a spravovat přiřazení rolí roviny dat (spravovaná místní RBAC. HSM).

Model oprávnění pro obě roviny používá stejnou syntaxi (RBAC), ale jsou využívány na různých úrovních a přiřazení rolí používá různé obory. Rovina správy RBAC je vynutila Azure Resource Manager, zatímco je neuplatněna rovina dat pomocí spravovaného HSM samotného.

> [!IMPORTANT]
> Udělení přístupu roviny správy objektu zabezpečení spravovanému modulu HSM jim neuděluje žádný přístup k rovině dat pro přístup k klíčům nebo k přiřazení role roviny dat spravovaná místní RBAC. Tato izolace je navržená tak, aby zabránila nechtěnému rozšiřování oprávnění, která mají vliv na přístup k klíčům uloženým ve spravovaném HSM

Například Správce předplatného (vzhledem k tomu, že mají oprávnění Přispěvatel pro všechny prostředky v rámci předplatného) může odstranit spravovaný modul HSM ve svém předplatném, ale pokud nemají přístup k rovině dat udělený výslovně prostřednictvím spravované místní správy HSM, nemůže získat přístup k klíčům nebo spravovat přiřazení rolí ve spravovaném modulu HSM a udělit mu tak přístup k rovině dat nebo ostatním uživatelům.

## <a name="azure-active-directory-authentication"></a>Ověřování služby Azure Active Directory

Když vytvoříte spravovaný modul HSM v rámci předplatného Azure, automaticky se přiřadí k tenantovi Azure Active Directory daného předplatného. Všechny volající v obou rovinách musí být zaregistrované v tomto tenantovi a ověřit pro přístup ke spravovanému modulu HSM.

Aplikace se ověřuje pomocí Azure Active Directory před voláním kterékoli roviny. Aplikace může použít jakoukoli [podporovanou metodu ověřování](../../active-directory/develop/authentication-vs-authorization.md) založenou na typu aplikace. Aplikace získá token pro prostředek v rovině, aby získal přístup. Prostředek je koncový bod v rovině pro správu nebo data na základě prostředí Azure. Aplikace používá token a odesílá požadavek REST API spravovanému koncovému bodu HSM. Pokud se chcete dozvědět víc, Projděte si [celý tok ověřování](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Použití jediného mechanismu ověřování pro obě roviny má několik výhod:

- Organizace můžou centrálně řídit přístup ke všem spravovaným hsmm v jejich organizaci.
- Pokud uživatel opustí, okamžitě ztratí přístup ke všem spravovaným HSM v organizaci.
- Organizace mohou ověřování přizpůsobit pomocí možností v Azure Active Directory, jako je například povolení služby Multi-Factor Authentication pro zvýšení zabezpečení.

## <a name="resource-endpoints"></a>Koncové body prostředků

Objekty zabezpečení přistupují k rovinám prostřednictvím koncových bodů. Ovládací prvky přístupu pro tyto dvě roviny pracují nezávisle. Chcete-li udělit aplikaci přístup k používání klíčů ve spravovaném modulu HSM, udělíte přístup k rovině dat pomocí spravované místní RBAC (HSM). Pokud chcete uživateli udělit přístup ke spravovanému prostředku HSM, abyste mohli vytvářet, číst, odstraňovat, přesouvat spravované HSM a upravovat další vlastnosti a značky, které použijete v Azure RBAC.

V následující tabulce jsou uvedeny koncové body pro řídicí a datové roviny.

| &nbsp;Rovina přístupu | Koncové body přístupu | Operace | Mechanismus řízení přístupu |
| --- | --- | --- | --- |
| Rovina správy | **Globální**<br> management.azure.com:443<br> | Vytváření, čtení, aktualizace, odstranění a přesun spravovaných HSM<br>Nastavení spravovaných značek HSM | Azure RBAC |
| Rovina dat | **Globální**<br> &lt;HSM-name &gt; . Vault.Azure.NET:443<br> | **Klíče** : dešifrování, šifrování,<br> rozbalení, zalomení, ověření, podepsání, získání, seznam, aktualizace, vytvoření, import, odstranění, zálohování, obnovení, vyprázdnění<br/><br/> **Správa rolí roviny dat (spravovaná místní RBAC)**_: seznam definic rolí, přiřazování rolí, odstraňování přiřazení rolí, definování vlastních rolí <br/> <br/>_ * zálohování a obnovení **: zálohování, obnovení, <br/> <br/> kontrolu stavu zálohování/obnovení** v doméně zabezpečení * *: stažení a nahrání domény zabezpečení | Spravovaná místní RBAC pro HSM |
|||||
## <a name="management-plane-and-azure-rbac"></a>Rovina správy a Azure RBAC

V rovině správy pomocí Azure RBAC autorizujete operace, které volající může spustit. V modelu RBAC má každé předplatné Azure instanci Azure Active Directory. Přístup k uživatelům, skupinám a aplikacím udělíte z tohoto adresáře. Přístup se uděluje pro správu prostředků v předplatném Azure, které používají model nasazení Azure Resource Manager. K udělení přístupu použijte [Azure Portal](https://portal.azure.com/), rozhraní příkazového [řádku Azure CLI](/cli/azure/install-classic-cli), [Azure PowerShell](/powershell/azureps-cmdlets-docs)nebo [Azure Resource Manager rozhraní REST API](/rest/api/authorization/roleassignments).

V rámci skupiny prostředků můžete vytvořit Trezor klíčů a spravovat přístup pomocí Azure Active Directory. Uživatelům nebo skupinám udělíte možnost spravovat trezory klíčů ve skupině prostředků. Přístup na konkrétní úroveň oboru udělíte tak, že jim přiřadíte příslušné role RBAC. Chcete-li uživateli udělit přístup ke správě trezorů klíčů, přiřaďte uživatele předdefinované `key vault Contributor` role v konkrétním oboru. Role RBAC může přiřadit tyto úrovně oborů:

- **Skupina pro správu** : role RBAC přiřazená na úrovni předplatného se vztahuje na všechna předplatná v této skupině pro správu.
- **Předplatné** : role RBAC přiřazená na úrovni předplatného se vztahuje na všechny skupiny prostředků a prostředky v rámci daného předplatného.
- **Skupina prostředků** : role RBAC přiřazená na úrovni skupiny prostředků se vztahuje na všechny prostředky v této skupině prostředků.
- **Konkrétní prostředek** : na tento prostředek se vztahuje role RBAC přiřazená pro konkrétní prostředek. V tomto případě je prostředkem konkrétní Trezor klíčů.

Existuje několik předdefinovaných rolí. Pokud předdefinovaná role nevyhovuje vašim potřebám, můžete definovat vlastní roli. Další informace naleznete v části [RBAC: předdefinované role](../../role-based-access-control/built-in-roles.md).

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Rovina dat a spravovaná místní RBAC pro HSM

Přístup k objektu zabezpečení udělíte oprávnění k provádění konkrétních operací s klíči přiřazením role. Pro každé přiřazení role potřebujete zadat roli a rozsah, přes který se toto přiřazení vztahuje. Pro spravované místní RBAC jsou dostupné dva obory.

- **"/" nebo "/Keys"** : rozsah úrovně HSM. Objekty zabezpečení přiřazené roli v tomto oboru můžou provádět operace definované v roli pro všechny objekty (klíče) ve spravovaném modulu HSM.
- **"/Keys/ &lt; klíč-název &gt; "** : obor úrovně klíče. Objekty zabezpečení přiřazené roli v tomto oboru můžou provádět operace definované v této roli jenom pro všechny verze zadaného klíče.

## <a name="next-steps"></a>Další kroky

- Úvodní kurz pro správce najdete v tématu [co je spravovaný modul HSM?](overview.md).
- Kurz správy rolí najdete v tématu [spravovaná místní RBAC](role-management.md) .
- Další informace o protokolování využití spravovaného protokolování HSM najdete v tématu [MANAGED HSM Logging](logging.md).