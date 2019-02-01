---
title: 'Synchronizace Azure AD Connect: Prevence náhodného odstranění | Dokumentace Microsoftu'
description: Toto téma popisuje funkci zabránit náhodnému odstranění (prevence náhodného odstranění) ve službě Azure AD Connect.
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
ms.openlocfilehash: 55f208b6fda85d82456694279cf22537a423782e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495475"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Synchronizace Azure AD Connect: Prevence náhodného odstranění
Toto téma popisuje funkci zabránit náhodnému odstranění (prevence náhodného odstranění) ve službě Azure AD Connect.

Při instalaci Azure AD Connect, zabránit náhodnému odstraní je ve výchozím nastavení povolená a nakonfigurovaná tak, aby neumožní export s více než 500 odstranění. Tato funkce slouží k ochraně před náhodnými změnami konfigurace a změnami vašeho místního adresáře, které by mohly ovlivnit mnoho uživatelů a dalších objektů.

## <a name="what-is-prevent-accidental-deletes"></a>Co je prevence náhodného odstranění
Běžné scénáře, až uvidíte mnoho odstraní patří:

* Změny [filtrování](how-to-connect-sync-configure-filtering.md) tam, kde celý [organizační jednotky](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) nebo [domény](how-to-connect-sync-configure-filtering.md#domain-based-filtering) byl zrušen výběr cesty.
* Odstraní se všechny objekty v organizační jednotce.
* Organizační jednotka se přejmenuje, takže všechny objekty, které jsou v ní obsažené, jsou mimo rozsah synchronizace.

Výchozí hodnota 500 objektů je možné měnit pomocí prostředí PowerShell pomocí `Enable-ADSyncExportDeletionThreshold`, který je součástí instalace s Azure Active Directory Connect modulu AD Sync. Měli byste nakonfigurovat tuto hodnotu, aby odpovídal zadané velikosti vaší organizace. Vzhledem k tomu, že Plánovač synchronizace spouští každých 30 minut, hodnota je počet odstraní viděli do 30 minut.

Pokud existuje příliš mnoho odstraní připravené nelze exportovat do služby Azure AD, zastaví se export a vám přijde e-mail následujícím způsobem:

![Zabránit náhodnému odstranění e-mailu](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hello (technický kontakt). (Chvíli) Služba synchronizace identit zjistil, že počet odstranění překročení této mezní hodnoty nakonfigurované odstranění (názvu organizace). (Číslo) Celkový počet objektů byly odeslány pro odstranění v této synchronizace identit spustit. To dosažená nebo Překročená prahová hodnota nakonfigurované odstranění objektů (číslo). Potřebujeme, abychom vám poskytli potvrzení, že odstranění by se měly zpracovat před jsme bude pokračovat. Podrobnosti najdete na ochrana proti nechtěným odstraněním pro další informace o chybě uvedených v tomto e-mailové zprávy.*
>
> 

Můžete taky zobrazit stav `stopped-deletion-threshold-exceeded` když se podíváte **Synchronization Service Manager** uživatelského rozhraní pro Export profile.
![Prevence náhodného odstranění uživatelského rozhraní Správce služby synchronizace](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Pokud jste to neočekávané, prozkoumat a provést opravné akce. Pokud chcete zobrazit, které objekty se chystáte odstranit, postupujte takto:

1. Spustit **synchronizační služba** z nabídky Start.
2. Přejděte na **konektory**.
3. Vyberte konektor s typem **Azure Active Directory**.
4. V části **akce** na pravé straně vyberte **Search Connector Space**.
5. V místní nabídce v části **oboru**vyberte **odpojen od** a vyberte čas v minulosti. Klikněte na **Hledat**. Tato stránka poskytuje přehled všech objektů má být odstraněn. Když kliknete na každou položku, můžete získat další informace o objektu. Můžete také kliknout na **nastavení sloupce** přidat další atributy, které se zobrazených v mřížce.

![Hledání prostoru konektoru](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

Pokud jsou všechny operace odstranění, potom postupujte takto:

1. Pokud chcete načíst aktuální prahová hodnota odstranění, spusťte rutinu prostředí PowerShell `Get-ADSyncExportDeletionThreshold`. Zadejte účet globálního správce Azure AD a heslo. Výchozí hodnota je 500.
2. Dočasně zakázat tuto ochranu a nechat tyto odstraní absolvovat, spusťte rutinu Powershellu: `Disable-ADSyncExportDeletionThreshold`. Zadejte účet globálního správce Azure AD a heslo.
   ![Přihlašovací údaje](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. S Azure konektoru služby Active Directory stále vybraná, vyberte akci **spustit** a vyberte **exportovat**.
4. Chcete-li znovu povolit ochranu, spusťte rutinu Powershellu: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Nahraďte hodnotu, kterou jste si všimli při načítání aktuální prahová hodnota odstranění 500. Zadejte účet globálního správce Azure AD a heslo.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
