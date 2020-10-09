---
title: U všech trezorů klíčů Azure se povolí obnovitelné odstranění | Microsoft Docs
description: Pomocí tohoto dokumentu můžete u všech trezorů klíčů přijmout obnovitelné odstranění.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: sudbalas
ms.openlocfilehash: 0e811cc219002c034afb968be760ce2c249b08f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825255"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Obnovitelné odstranění bude povolené u všech trezorů klíčů.

> [!WARNING]
> Zásadní **Změna**: možnost odsouhlasení s tichou úpravou bude po konci roku zastaralá a ochrana proti softwarovému odstranění se automaticky zapne pro všechny trezory klíčů.  Azure Key Vault uživatelé a Správci by měli pro svoje trezory klíčů okamžitě povolit obnovitelné odstranění.
>
> V případě spravovaného modulu HSM je ve výchozím nastavení povolené obnovitelné odstranění a nedá se zakázat.

Při odstranění tajného klíče z trezoru klíčů bez ochrany proti tichému odstranění se tajný kód trvale odstraní. Uživatelé můžou v současné době během vytváření trezoru klíčů odhlásit nějaké obnovitelné odstranění, ale pokud chtějí chránit vaše tajná klíč před náhodným nebo škodlivým odstraněním, Microsoft brzo u **všech** trezorů klíčů povolí ochranu před náhodným odstraněním a uživatelé už nebudou mít možnost odhlásit nebo zapnout obnovitelné odstranění.

:::image type="content" source="../media/softdeletediagram.png" alt-text="<alternativní text>":::

Úplné informace o funkci obnovitelného odstranění najdete v tématu [přehled Azure Key Vaultho obnovitelného odstranění](soft-delete-overview.md).

## <a name="how-do-i-respond-to-breaking-changes"></a>Návody reagovat na průlomové změny

Objekt trezoru klíčů nelze vytvořit se stejným názvem jako objekt trezoru klíčů ve stavu undeleteded.  Pokud například odstraníte klíč s názvem `test key` trezoru klíčů a, nebudete moci vytvořit nový klíč s názvem `test key` v trezoru klíčů a, dokud nebude odstraněn objekt, který je odstraněn `test key` .

### <a name="application-changes"></a>Změny aplikace

Pokud vaše aplikace předpokládá, že není povolené obnovitelné odstranění, a očekává, že odstraněné tajné názvy nebo trezory klíčů jsou k dispozici pro okamžité opakované použití, bude vaše logika aplikace muset provést následující změny, aby bylo možné tuto změnu přijmout.

1. Odstraní původní Trezor klíčů nebo tajný klíč.
2. Vyprázdnit Trezor klíčů nebo tajný klíč ve stavu obnovitelného odstranění.
3. Čekání – Okamžité opětovné vytvoření může způsobit konflikt.
4. Vytvořte znovu Trezor klíčů se stejným názvem.
5. Implementujte znovu, pokud operace vytvoření stále způsobí chybu konfliktu názvů, může trvat až 10 minut, než se záznamy DNS aktualizují v nejhorším případě.

### <a name="administration-changes"></a>Změny správy

Objekty zabezpečení, které potřebují přístup k trvale odstraněným tajným klíčům, musí mít udělena další oprávnění zásad přístupu k vyprázdnit tyto tajné klíče a trezor klíčů.

Pokud máte v trezorech klíčů Azure Policy, že je vypnutí obnovitelného odstranění vypnuté, bude nutné tuto zásadu Zakázat.  Tento problém možná budete muset vyřešit u správce, který řídí zásady Azure použité pro vaše prostředí. Pokud tato zásada není zakázaná, může dojít ke ztrátě schopnosti vytvářet nové trezory klíčů v oboru použitých zásad.

Pokud vaše organizace podléhá zákonným požadavkům na dodržování předpisů a neumožňuje odstraňovat trezory klíčů a tajné klíče, aby zůstaly v obnovitelném stavu, budete po delší dobu muset upravit dobu uchování obnovitelného odstranění, která se dá nakonfigurovat mezi 7 až 90 dny, aby splňovala standardy vaší organizace.

## <a name="procedures"></a>Procedury

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Auditujte trezory klíčů a zkontrolujte, jestli je povolené obnovitelné odstranění.

1. Přihlaste se k webu Azure Portal.
2. Vyhledejte "Azure Policy".
3. Vyberte definice.
4. V části Kategorie vyberte v filtru možnost "Key Vault".
5. Zaškrtněte políčko Key Vault objekty by měly být obnovitelné.
6. Klikněte na přiřadit.
7. Nastavte obor na své předplatné.
8. Vyberte "zkontrolovat + vytvořit".
9. V nástroji může trvat až 24 hodin, než se úplná kontrola vašeho prostředí dokončí.
10. V okně Azure Policy klikněte na "dodržování předpisů".
11. Vyberte zásadu, kterou jste použili.

Nyní byste měli mít možnost filtrovat a zjistit, které z vašich trezorů klíčů mají povolené obnovitelné odstranění (kompatibilní prostředky) a které trezory klíčů nemají povolené obnovitelné odstranění (nekompatibilní prostředky).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Zapnout obnovitelné odstranění pro existující Trezor klíčů

1. Přihlaste se k webu Azure Portal.
2. Vyhledejte Key Vault.
3. V části nastavení vyberte vlastnosti.
4. V části obnovitelné odstranění vyberte přepínač, který odpovídá příkazu "Povolit obnovení tohoto trezoru a jeho objektů".
5. Nastavte dobu uchování pro obnovitelné odstranění.
6. Vyberte Save (Uložit).

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Udělení oprávnění k vyprázdnění zásad přístupu k objektu zabezpečení

1. Přihlaste se k webu Azure Portal.
2. Vyhledejte Key Vault.
3. V části nastavení vyberte zásady přístupu.
4. Vyberte instanční objekt, ke kterému chcete udělit přístup.
5. U každého rozevíracího seznamu v části klíčová, tajná a oprávnění certifikátů přejděte dolů na "privilegované operace" a vyberte oprávnění "Vyprázdnit".

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-this-change-affect-me"></a>Má tato změna vliv na mě?

Pokud už máte zapnuté obnovitelné odstranění, nebo pokud neodstraníte a znovu nevytvoříte objekty trezoru klíčů se stejným názvem, nebudete si pravděpodobně všimnout žádné změny v chování trezoru klíčů.

Pokud máte aplikaci, která často odstraňuje a znovu vytváří objekty trezoru klíčů se stejnými zásadami vytváření názvů, budete muset provést změny v logice aplikace, abyste zachovali očekávané chování. Přečtěte si prosím "Návody reagovat na průlomové změny?" výše v části.

### <a name="how-do-i-benefit-from-this-change"></a>Návody výhodou tuto změnu?

Ochrana před náhodným odstraněním umožní vaší organizaci zvýšit úroveň ochrany před náhodným nebo úmyslným odstraněním. Jako správce trezoru klíčů můžete omezit přístup k oprávněním k obnovení i k vyprázdnit.

Pokud uživatel omylem odstraní Trezor klíčů nebo tajný klíč, můžete jim udělit přístupová oprávnění k obnovení tajného klíče bez vytvoření rizika, které trvale odstraní tajný klíč nebo Trezor klíčů. Tento proces samoobslužného procesu minimalizuje čas ve vašem prostředí a zaručuje dostupnost vašich tajných kódů.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Návody zjistit, jestli je potřeba provést akci?

Postupujte prosím podle kroků uvedených v části Postup pro audit vašich trezorů klíčů, abyste zkontrolovali, jestli je Soft-Delete zapnutá. Tato změna ovlivní všechny trezory klíčů, které nemají zapnuté obnovitelné odstranění. Další nástroje, které vám pomůžou auditovat, budou brzy dostupné a tento dokument se aktualizuje.

### <a name="what-action-do-i-need-to-take"></a>Jakou akci Potřebuji udělat?

Ujistěte se, že není nutné provádět změny v logice aplikace. Až to ověříte, zapněte u všech vašich trezorů klíčů obnovitelné odstranění. Tím zajistíte, že nebudete mít vliv na zásadní změnu, pokud je u všech trezorů klíčů na konci roku zapnuté obnovitelné odstranění.

### <a name="by-when-do-i-need-to-take-action"></a>Kdy je potřeba provést akci?

Obnovitelné odstranění bude zapnuté pro všechny trezory klíčů na konci roku. Abyste se ujistili, že vaše aplikace nebudou ovlivněny, zapněte v trezorech klíčů obnovitelné odstranění, a to co nejdříve.

## <a name="what-will-happen-if-i-dont-take-any-action"></a>Co se stane, když neprovedem žádnou akci?

Pokud neprovedete žádnou akci, obnovitelné odstranění se automaticky zapne pro všechny vaše trezory klíčů na konci roku. To může vést k chybám konfliktů, pokud se pokusíte odstranit objekt trezoru klíčů a znovu ho vytvořit se stejným názvem, aniž byste ho nejdřív odstranili ze stavu podmíněně odstraněno. To může způsobit selhání aplikací nebo automatizace.

## <a name="next-steps"></a>Další kroky

- Kontaktujte nás s případnými dotazy týkajícími se této změny v [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Přečtěte si [Přehled obnovitelného odstranění](soft-delete-overview.md)
