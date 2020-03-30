---
title: 'Synchronizace služby Azure AD Connect: Zabraňte náhodnému odstranění | Dokumenty společnosti Microsoft'
description: Toto téma popisuje funkci zabránit náhodnému odstranění (zabránění nechtěnému odstranění) ve službě Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71827129"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Synchronizace Azure AD Connect: Prevence náhodného odstranění
Toto téma popisuje funkci zabránit náhodnému odstranění (zabránění nechtěnému odstranění) ve službě Azure AD Connect.

Při instalaci Služby Azure AD Connect je ve výchozím nastavení povoleno zabránit nechtěnému odstranění a nakonfigurováno tak, aby neumožňovalo export s více než 500 odstraněními. Tato funkce slouží k ochraně před náhodnými změnami konfigurace a změnami vašeho místního adresáře, které by mohly ovlivnit mnoho uživatelů a dalších objektů.

## <a name="what-is-prevent-accidental-deletes"></a>Co je zabránit náhodnému odstranění
Běžné scénáře, kdy se zobrazí mnoho odstranění patří:

* Změní [filtrování,](how-to-connect-sync-configure-filtering.md) kde není vybraná celá [výuce](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) nebo [doména.](how-to-connect-sync-configure-filtering.md#domain-based-filtering)
* Odstraní se všechny objekty v organizační jednotce.
* Organizační jednotka se přejmenuje, takže všechny objekty, které jsou v ní obsažené, jsou mimo rozsah synchronizace.

Výchozí hodnotu 500 objektů lze změnit `Enable-ADSyncExportDeletionThreshold`pomocí prostředí PowerShell pomocí , který je součástí modulu AD Sync nainstalovaného pomocí služby Azure Active Directory Connect. Tuto hodnotu byste měli nakonfigurovat tak, aby odpovídala velikosti vaší organizace. Vzhledem k tomu, že plánovač synchronizace běží každých 30 minut, hodnota je počet odstranění vidět během 30 minut.

Pokud existuje příliš mnoho odstranění připraveno exportovat do Azure AD, export se zastaví a obdržíte e-mail takto:

![Zabránit náhodnému odstranění e-mailu](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Dobrý den (technický kontakt). Služba synchronizace identity (čas) zjistila, že počet odstranění překročil nakonfigurovanou prahovou hodnotu odstranění (název organizace). V tomto spuštění synchronizace identity bylo odesláno k odstranění celkem (čísel) objektů. Tato hodnota splnila nebo překročila nakonfigurovanou prahovou hodnotu odstranění (čísel) objektů. Potřebujeme, abyste nám potvrdili, že tato odstranění by měla být zpracována dříve, než budeme pokračovat. Další informace o chybě uvedené v této e-mailové zprávě naleznete v části Zabránění nechtěnému odstranění.*
>
> 

Stav `stopped-deletion-threshold-exceeded` můžete také zobrazit, když se podíváte do uznatového režimu **Správce služeb synchronizace** pro profil exportu.
![Zabránit náhodnému odstranění u](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Pokud to bylo neočekávané, pak prozkoumat a přijmout nápravná opatření. Chcete-li zjistit, které objekty mají být odstraněny, postupujte takto:

1. **Spusťte službu synchronizace** z nabídky Start.
2. Přejděte na **konektory**.
3. Vyberte konektor s typem **Azure Active Directory**.
4. V části **Akce** vpravo vyberte **Možnost Hledat mezeru v spojnici**.
5. V automaticky otevírané mamince v části **Obor**vyberte **Možnost Odpojeno od** a vyberte čas v minulosti. Klikněte na **Vyhledat**. Tato stránka poskytuje zobrazení všech objektů, které mají být odstraněny. Kliknutím na jednotlivé položky můžete získat další informace o objektu. Můžete také klepnout na **Nastavení sloupce a** přidat další atributy, které budou viditelné v mřížce.

![Hledat mezeru v spojnici](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Pokud si nejste jisti, že všechny odstraní jsou žádoucí, a chcete jít dolů bezpečnější cestou. Rutina prostředí PowerShell : `Enable-ADSyncExportDeletionThreshold` můžete nastavit novou prahovou hodnotu, nikoli zakázat prahovou hodnotu, která by mohla umožnit nežádoucí odstranění. 

## <a name="if-all-deletes-are-desired"></a>Pokud jsou požadována všechna odstranění
Pokud jsou požadovány všechny odstraní, postupujte takto:

1. Chcete-li načíst aktuální prahovou hodnotu `Get-ADSyncExportDeletionThreshold`odstranění, spusťte rutinu prostředí PowerShell . Zadejte účet globálního správce Azure AD a heslo. Výchozí hodnota je 500.
2. Chcete-li tuto ochranu dočasně zakázat a nechat tyto odstranění `Disable-ADSyncExportDeletionThreshold`projít, spusťte rutinu prostředí PowerShell: . Zadejte účet globálního správce Azure AD a heslo.
   ![Přihlašovací údaje](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Pokud je konektor Azure Active Directory stále vybraný, vyberte akci **Spustit** a vyberte **Exportovat**.
4. Chcete-li ochranu znovu povolit, spusťte rutinu prostředí PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Nahraďte hodnotu 500 hodnotou, kterou jste si všimli při načítání aktuální prahové hodnoty odstranění. Zadejte účet globálního správce Azure AD a heslo.

## <a name="next-steps"></a>Další kroky
**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
