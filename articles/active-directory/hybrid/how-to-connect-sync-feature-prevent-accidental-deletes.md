---
title: 'Azure AD Connect synchronizace: zabránit náhodným odstraněním | Microsoft Docs'
description: Toto téma popisuje funkci prevence náhodného odstranění (prevence nechtěného odstranění) v Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f3109b4c87e25444629ca25411894eab8a9d56
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827129"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect synchronizace: zabránit náhodným odstraněním
Toto téma popisuje funkci prevence náhodného odstranění (prevence nechtěného odstranění) v Azure AD Connect.

Při instalaci Azure AD Connect zabránit náhodnému odstranění, je ve výchozím nastavení povolené a nakonfigurované tak, aby se export s více než 500 odstraňováním nepovoloval. Tato funkce je navržená tak, aby vás chránila před nechtěnými změnami konfigurace a změnami v místním adresáři, které by mohly ovlivnit mnoho uživatelů a dalších objektů.

## <a name="what-is-prevent-accidental-deletes"></a>Co je zabránit náhodným odstraněním
Mezi běžné scénáře, kdy vidíte mnoho odstranění, patří:

* Změny [filtrování](how-to-connect-sync-configure-filtering.md) , u kterých není vybraná celá [organizační jednotka](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) nebo [doména](how-to-connect-sync-configure-filtering.md#domain-based-filtering) .
* Odstraní se všechny objekty v organizační jednotce.
* Organizační jednotka se přejmenuje, aby všechny objekty v ní byly považovány za mimo rozsah synchronizace.

Výchozí hodnotu 500 objektů lze změnit pomocí prostředí PowerShell pomocí `Enable-ADSyncExportDeletionThreshold`, což je součást modulu AD Sync nainstalovaného Azure Active Directory Connect. Tuto hodnotu byste měli nakonfigurovat tak, aby odpovídala velikosti vaší organizace. Vzhledem k tomu, že se Plánovač synchronizace spouští každých 30 minut, jedná se o počet odstranění, který se zobrazuje do 30 minut.

Pokud je pro export do služby Azure AD moc málo mazání, export se zastaví a dostanete e-mail podobný tomuto:

![Zabránit náhodnému odstranění e-mailu](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hello (technický kontakt). V (čas) Služba Synchronizace identity zjistila, že počet odstranění překročil nakonfigurovanou prahovou hodnotu pro odstranění (název organizace). V tomto Synchronizace identity spuštění bylo odesláno celkem objektů (počet) k odstranění. Tím byla splněna nebo překročena nakonfigurovaná prahová hodnota pro odstranění objektů (počet). Abychom mohli pokračovat, musíme vám poskytnout potvrzení, že by se tato odstranění měla zpracovat. Další informace o chybě uvedené v této e-mailové zprávě najdete v tématu prevence náhodného odstranění.*
>
> 

Stav `stopped-deletion-threshold-exceeded` můžete zobrazit také při hledání v uživatelském rozhraní **Synchronization Service Manager** pro export profilu.
@no__t – 0Prevent nechtěné odstranění Service Manager uživatelského rozhraní pro synchronizaci @ no__t-1

Pokud se to neočekávalo, prozkoumejte a proveďte nápravné akce. Chcete-li zjistit, které objekty budou smazány, postupujte takto:

1. Spusťte **synchronizační službu** z nabídky Start.
2. Přejít na **konektory**.
3. Vyberte konektor s typem **Azure Active Directory**.
4. V části **Akce** vpravo vyberte **Hledat místo v konektoru**.
5. V místní nabídce v části **obor**vyberte **Odpojit od** a vyberte čas v minulosti. Klikněte na tlačítko **Hledat**. Tato stránka poskytuje zobrazení všech objektů, které mají být odstraněny. Kliknutím na jednotlivé položky můžete získat další informace o objektu. Můžete také kliknout na **Nastavení sloupce** a přidat další atributy, které mají být viditelné v mřížce.

![Hledat místo v konektoru](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Pokud si nejste jistí, jestli jsou všechna odstranění požadovaná a chtějí přejít na bezpečnější trasu. Pomocí rutiny PowerShellu: `Enable-ADSyncExportDeletionThreshold` můžete nastavit novou prahovou hodnotu místo zakázání prahové hodnoty, která by mohla umožňovat nežádoucí odstranění. 

## <a name="if-all-deletes-are-desired"></a>Pokud jsou všechna odstranění požadovaná
Pokud jsou všechna odstranění požadovaná, proveďte následující akce:

1. Pokud chcete načíst aktuální prahovou hodnotu pro odstranění, spusťte rutinu PowerShellu `Get-ADSyncExportDeletionThreshold`. Zadejte účet globálního správce služby Azure AD a heslo. Výchozí hodnota je 500.
2. Pokud chcete tuto ochranu dočasně zakázat a nechat tyto operace odstranit, spusťte rutinu PowerShellu: `Disable-ADSyncExportDeletionThreshold`. Zadejte účet globálního správce služby Azure AD a heslo.
   @no__t – 0Credentials @ no__t-1
3. Když je konektor Azure Active Directory stále vybraný, vyberte akci **Spustit** a vyberte **exportovat**.
4. Pokud chcete ochranu znovu povolit, spusťte rutinu PowerShellu: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Nahraďte 500 hodnotou, kterou jste si všimli při načítání aktuální prahové hodnoty pro odstranění. Zadejte účet globálního správce služby Azure AD a heslo.

## <a name="next-steps"></a>Další kroky
**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md)
