---
title: Povolit obnovitelné odstranění u všech objektů trezoru klíčů – Azure Key Vault | Microsoft Docs
description: Tento dokument použijte k přijetí obnovitelného odstranění pro všechny trezory klíčů a k provádění změn aplikace a správy, aby nedocházelo k chybám konfliktů.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: b96f2ca4f925846bd252e5cfd35088d832f5c216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98572863"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Obnovitelné odstranění bude povolené u všech trezorů klíčů.

> [!WARNING]
> Zásadní změna: možnost Odhlásit se z slabého odstranění už brzy přestane. Azure Key Vault uživatelé a Správci by měli pro svoje trezory klíčů okamžitě povolit obnovitelné odstranění.
>
> Pro Azure Key Vault spravovaný modul HSM je ve výchozím nastavení povolené obnovitelné odstranění a nedá se zakázat.

Při odstranění tajného klíče z trezoru klíčů bez ochrany proti tichému odstranění se tajný kód trvale odstraní. Uživatelé můžou během vytváření trezoru klíčů v současné době odhlásit obnovitelné odstranění. Microsoft ale brzy povolí ochranu před náhodným odstraněním u všech trezorů klíčů za účelem ochrany tajných kódů před náhodným nebo úmyslným odstraněním uživatelem. Uživatelé již nebudou moci odhlásit nebo vypnout obnovitelné odstranění.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagram znázorňující, jak se odstraní Trezor klíčů s ochranou pomocí obnovitelného odstranění i bez ochrany proti tichému odstranění.":::

Úplné informace o funkci obnovitelného odstranění najdete v tématu [přehled Azure Key Vaultho obnovitelného odstranění](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Může moje aplikace pracovat s povoleným tichým odstraněním?

> [!Important] 
> Před zapnutím obnovitelného odstranění pro trezory klíčů si pečlivě Projděte tyto informace.

Názvy trezoru klíčů jsou globálně jedinečné. Názvy tajných kódů uložených v trezoru klíčů jsou také jedinečné. Nebudete moct znovu použít název trezoru klíčů nebo objektu trezoru klíčů, který existuje ve stavu obnovitelného odstranění. 

Například pokud vaše aplikace programově vytvoří Trezor klíčů s názvem "trezor a" a později odstraní "trezor A", bude Trezor klíčů přesunut do stavu odstraněno. Vaše aplikace nebude moct znovu vytvořit další Trezor klíčů s názvem "trezor A", dokud nebude Trezor klíčů vymazán ze stavu obnovitelného odstranění. 

Také Pokud vaše aplikace vytvoří klíč s názvem `test key` "trezor a" a později tento klíč odstraní, aplikace nebude moci vytvořit nový klíč s názvem `test key` "trezor a", dokud nebude `test key` objekt vymazán ze stavu obnovitelného odstranění. 

Když se pokusíte odstranit objekt trezoru klíčů a znovu ho vytvořit se stejným názvem, aniž byste ho museli vymazat ze stavu, který je podmíněně odstranil, může dojít k chybám konfliktu. Tyto chyby můžou způsobit selhání aplikací nebo automatizace. Než provedete následující požadované změny aplikace a správy, obraťte se na svého vývojářského týmu. 

### <a name="application-changes"></a>Změny aplikace

Pokud vaše aplikace předpokládá, že obnovitelné odstranění není povoleno a očekává, že odstraněné tajné názvy nebo trezory klíčů jsou k dispozici pro okamžité opakované použití, bude nutné provést následující změny v logice aplikace.

1. Odstraňte původní Trezor klíčů nebo tajný klíč.
1. Vyprázdnit Trezor klíčů nebo tajný klíč ve stavu obnovitelného odstranění.
1. Počkejte, až se vyčištění dokončí. Okamžité opětovné vytvoření může způsobit konflikt.
1. Vytvořte znovu Trezor klíčů se stejným názvem.
1. Pokud operace vytvoření stále způsobí chybu konfliktu názvů, zkuste znovu vytvořit Trezor klíčů. Aktualizace záznamů Azure DNS může trvat až 10 minut, než se v nejhorším scénáři doplní.

### <a name="administration-changes"></a>Změny správy

Objekty zabezpečení, které potřebují přístup k trvale odstraněným tajným klíčům, musí mít pro vyprázdnění těchto tajných klíčů a trezoru klíčů uděleno více oprávnění zásad přístupu.

Zakažte všechny zásady Azure u trezorů klíčů, které zajistí, že obnovitelné odstranění je vypnuté. Tento problém možná budete muset vyřešit u správce, který řídí zásady Azure použité pro vaše prostředí. Pokud tato zásada není zakázaná, může dojít ke ztrátě schopnosti vytvářet nové trezory klíčů v oboru použitých zásad.

Pokud vaše organizace podléhá zákonným požadavkům na dodržování předpisů a neumožňuje odstraňovat trezory klíčů a tajné klíče, aby zůstaly v obnovitelném stavu po delší dobu, budete muset upravit dobu uchování obnovitelného odstranění, aby splňovala standardy vaší organizace. Dobu uchovávání můžete nakonfigurovat na poslední 7 až 90 dní.

## <a name="procedures"></a>Procedury

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Auditujte trezory klíčů a zkontrolujte, jestli je povolené obnovitelné odstranění.

1. Přihlaste se k webu Azure Portal.
1. Vyhledejte **Azure Policy**.
1. Vyberte **definice**.
1. V části **kategorie** vyberte ve filtru **Key Vault** .
1. Vyberte **Key Vault má mít povolené zásady pro obnovitelné odstranění** .
1. Vyberte **Přiřadit**.
1. Nastavte obor na své předplatné.
1. Ujistěte se, že je efekt zásady nastavený na **audit**.
1. Vyberte **Zkontrolovat a vytvořit**. Úplné prohledávání vašeho prostředí může trvat až 24 hodin.
1. V podokně **Azure Policy** vyberte možnost **dodržování předpisů**.
1. Vyberte zásadu, kterou jste použili.

Teď můžete filtrovat a zjistit, které trezory klíčů mají povolené obnovitelné odstranění (kompatibilní prostředky) a které trezory klíčů nemají povolené obnovitelné odstranění (nekompatibilní prostředky).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Zapnutí obnovitelného odstranění pro existující Trezor klíčů

1. Přihlaste se k webu Azure Portal.
1. Vyhledejte svůj Trezor klíčů.
1. V části **Nastavení** vyberte **vlastnosti** .
1. V části **obnovitelné odstranění** vyberte možnost **Povolit obnovení tohoto trezoru a jeho objektů** .
1. Nastavte dobu uchování pro obnovitelné odstranění.
1. Vyberte **Uložit**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Udělení oprávnění k vyprázdnění zásad přístupu k objektu zabezpečení

1. Přihlaste se k webu Azure Portal.
1. Vyhledejte svůj Trezor klíčů.
1. V části **Nastavení** vyberte **zásady přístupu** .
1. Vyberte instanční objekt, ke kterému chcete udělit přístup.
1. V rámci každé rozevírací nabídky v části **klíčová**, **tajná** a **oprávnění certifikátu** přecházejte, dokud neuvidíte **privilegované operace**. Vyberte oprávnění **vyprázdnit** .

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-this-change-affect-me"></a>Má tato změna vliv na mě?

Pokud už máte zapnuté obnovitelné odstranění, nebo pokud neodstraníte a znovu nevytvoříte objekty trezoru klíčů se stejným názvem, nebudete si pravděpodobně všimnout žádné změny v chování trezoru klíčů.

Pokud máte aplikaci, která často odstraňuje a znovu vytváří objekty trezoru klíčů se stejnými zásadami vytváření názvů, budete muset provést změny v logice aplikace, abyste zachovali očekávané chování. Viz část [změny aplikace](#application-changes) v tomto článku.

### <a name="how-do-i-benefit-from-this-change"></a>Návody výhodou tuto změnu?

Ochrana před náhodným odstraněním poskytuje vaší organizaci jinou vrstvu ochrany proti náhodnému nebo škodlivému odstranění. Jako správce trezoru klíčů můžete omezit přístup k oprávněním k obnovení i k vyprázdnit.

Pokud uživatel omylem odstraní Trezor klíčů nebo tajný klíč, můžete jim udělit přístupová oprávnění k obnovení tajného klíče bez vytvoření rizika, které trvale odstraní tajný klíč nebo Trezor klíčů. Tento proces samostatně obsluhy minimalizuje prostoje ve vašem prostředí a zajistí dostupnost vašich tajných kódů.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Návody zjistit, jestli je potřeba provést akci?

Postupujte podle kroků v části [audit vašich trezorů klíčů a zkontrolujte, zda je v tomto článku k dispozici obnovitelné odstranění](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) . Tato změna bude mít vliv na všechny trezory klíčů, u kterých není zapnuté obnovitelné odstranění.

### <a name="what-action-do-i-need-to-take"></a>Jakou akci Potřebuji udělat?

Až ověříte, že nemusíte dělat změny v logice vaší aplikace, zapněte u všech vašich trezorů klíčů obnovitelné odstranění.

### <a name="when-do-i-need-to-take-action"></a>Kdy Potřebuji udělat akci?

Abyste se ujistili, že vaše aplikace nejsou ovlivněné, zapněte v trezorech klíčů obnovitelné odstranění, co nejrychleji.

## <a name="next-steps"></a>Další kroky

- Kontaktujte nás s případnými dotazy k této změně v [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Přečtěte si [Přehled obnovitelného odstranění](soft-delete-overview.md).
