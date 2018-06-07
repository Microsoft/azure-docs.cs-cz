---
title: Konektory v uživatelském rozhraní Azure AD Synchronization Service Manager | Microsoft Docs
description: Porozumět kartě konektory ve Správci služby synchronizace Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b3c8925b213543d33f81c113b08e9601f2780bde
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593148"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Použití konektorů s Azure AD Connect Sync Správce služeb

![Správce synchronizace služby](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

Na kartě konektory slouží ke správě všech systémů, synchronizační modul je připojena k.

## <a name="connector-actions"></a>Konektor akce
| Akce | Poznámka |
| --- | --- |
| Vytvořit |Nepoužívejte. Pro připojení k další doménové struktury AD, použijte Průvodce instalací. |
| Vlastnosti |Použít pro filtrování organizační jednotky a domény. |
| [Odstranění](#delete) |Používá se buď odstranit data v prostoru konektoru nebo odstranit připojení k doménové struktuře. |
| [Konfigurace profilů spuštění](#configure-run-profiles) |S výjimkou domény filtrování, co zde konfigurovat. Tato akce můžete použít zobrazíte už nakonfigurované profilů spuštění. |
| Spusťte |Používá ke spuštění jednorázové spuštění profilu. |
| Zastavit |Zastaví konektor aktuálně spuštěných profil. |
| Export konektoru |Nepoužívejte. |
| Konektor pro import |Nepoužívejte. |
| Aktualizace konektoru |Nepoužívejte. |
| Aktualizovat schéma |Aktualizuje v mezipaměti schématu. Je upřednostňovanou možnost v Průvodci instalací místo toho chcete použít, od které také aktualizace synchronizovat pravidla. |
| [Hledání prostoru konektoru](#search-connector-space) |Použít k vyhledání objektů a [podle objektu a jeho data prostřednictvím systému](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Odstranění
Akci odstranění se používá pro dvě různé věci.  
![Správce synchronizace služby](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

Možnost **odstranit pouze prostor konektoru** odeberete všechna data, ale ponechat konfiguraci.

Možnost **odstranit konektor a konektor místo** odebere data a konfigurace. Tato možnost se používá, když nechcete připojit k doménové struktuře už.

Obě možnosti synchronizovat všechny objekty a aktualizovat úložiště metaverse objekty. Tato akce je dlouhotrvající operace.

### <a name="configure-run-profiles"></a>Konfigurace profilů spuštění
Tato možnost umožňuje najdete v části profilů spuštění, který je nakonfigurován pro konektor.

![Správce synchronizace služby](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Hledání prostoru konektoru
Akce místa konektoru vyhledávání je užitečné k nalezení objektů a dat potíže.

![Správce synchronizace služby](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Začněte výběrem **oboru**. Můžete hledat na základě dat (relativního Rozlišujícího, rozlišující název, ukotvení podstromu), nebo stavu objektu (všechny ostatní možnosti).  
![Správce synchronizace služby](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Pokud například provedete hledání podstromu, získáte všechny objekty v jedné organizační jednotce.  
![Správce synchronizace služby](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
Z této tabulky můžete vybrat objekt, vyberte **vlastnosti**, a [na něho kliknout,](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) z prostoru konektoru zdroje, přes úložiště metaverse a do prostoru konektoru cíl.

### <a name="changing-the-ad-ds-account-password"></a>Změna hesla účtu služby AD DS
Pokud změníte heslo k účtu, služba synchronizace už nebude moct importu a exportu změny místní AD.   Může se zobrazit následující:

- Krok importu a exportu pro službu AD connector selže s chybou "bez pověření start".
- V prohlížeči událostí systému Windows v protokolu událostí aplikace obsahuje chybu s 6000 ID události a zprávou "agenta pro správu"contoso.com"Nepodařilo se spustit, protože přihlašovací údaje nebyly platné."

Chcete-li problém vyřešit, aktualizujte uživatelský účet služby AD DS pomocí následující:


1. Spusťte Synchronization Service Manager (Služba synchronizace → START).
</br>![Správce synchronizace služby](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)
2. Přejděte na **konektory** kartě.
3. Vyberte konektor AD, která je nakonfigurována pro použití účtu služby AD DS.
4. V části Akce, vyberte **vlastnosti**.
5. V dialogovém okně automaticky otevírané okno Vyberte možnost připojit k doménové struktuře služby Active Directory:
6. Určuje název doménové struktury odpovídající místní AD.
7. Určuje, uživatelské jméno účtu služby AD DS používán k synchronizaci.
8. Zadejte nové heslo účtu služby AD DS do textového pole hesla ![Azure AD Connect Sync šifrování klíče nástroj](media/active-directory-aadconnectsync-encryption-key/key6.png)
9. Kliknutím na tlačítko OK uložte nové heslo a restartujte synchronizační službu odebrat staré heslo z mezipaměti paměti.



## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
