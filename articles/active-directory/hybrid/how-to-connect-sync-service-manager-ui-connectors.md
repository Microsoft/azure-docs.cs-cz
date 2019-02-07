---
title: Konektory v Uživatelském rozhraní služby Azure AD Synchronization Service Manager | Dokumentace Microsoftu
description: Vysvětlení kartou konektorů v Synchronization Service Manager pro Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40e519495b8a245f72e51167f5807f9c585cb344
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817542"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Používání konektorů s Azure AD Connect Sync Service Manager

![Správce synchronizace služby](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Na kartě konektory slouží ke správě všech systémů, který synchronizační modul je připojen k.

## <a name="connector-actions"></a>Akce konektorů
| Akce | Poznámka |
| --- | --- |
| Vytvořit |Nepoužívejte. Pro připojení k další doménové struktury AD, použijte Průvodce instalací. |
| Vlastnosti |Používá pro domény a filtrování podle organizačních jednotek. |
| [Odstranění](#delete) |Použít můžete buď odstranit data v prostoru konektoru nebo odstranit připojení k doménové struktuře. |
| [Konfigurace profilů spuštění](#configure-run-profiles) |S výjimkou domény filtrování, nic ke konfiguraci sem. Tato akce můžete zobrazit už nakonfigurované profilů spuštění. |
| Spusťte |Používá ke spuštění jednorázové spuštění profilu. |
| Zastavit |Zastaví konektor profil, který aktuálně běží. |
| Export konektoru |Nepoužívejte. |
| Konektor pro import |Nepoužívejte. |
| Aktualizovat konektor |Nepoužívejte. |
| Aktualizovat schéma |Aktualizuje v mezipaměti schématu. Je upřednostňována před místo toho použijte možnost v Průvodci instalací od, který také aktualizace synchronizovat pravidla. |
| [Hledání prostoru konektoru](#search-connector-space) |Používá k nalezení objektů a postupujte podle objektu a jeho data prostřednictvím systému. |

### <a name="delete"></a>Odstranění
Pro dvě různé věci se nepoužije akce odstranění.  
![Správce synchronizace služby](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Možnost **odstranit pouze prostoru konektoru** odeberete všechna data, ale zachovat konfiguraci.

Možnost **konektor odstranit a konektoru místo** odebere data a konfiguraci. Tato možnost se používá, pokud nechcete připojit k doménové struktuře už.

Obě možnosti synchronizovat všechny objekty a aktualizace objektů úložiště metaverse. Tato akce je dlouho běžící operace.

### <a name="configure-run-profiles"></a>Konfigurace profilů spuštění
Tato možnost umožňuje zobrazit profilů spuštění pro konektor nakonfigurovali.

![Správce synchronizace služby](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Hledání prostoru konektoru
Akce hledání konektoru místo je užitečná k nalezení objektů a řešení potíží s data.

![Správce synchronizace služby](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Začněte výběrem **oboru**. Můžete vyhledávat na základě dat (relativní rozlišující název domény, ukotvení Podstromě), nebo stavu objektu (všechny ostatní možnosti).  
![Správce synchronizace služby](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Pokud například provedete hledání podstromu, získáte všechny objekty v jedné organizační jednotce.  
![Správce synchronizace služby](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Z této tabulky můžete vybrat objekt, vyberte **vlastnosti**, a [na něho](tshoot-connect-object-not-syncing.md) z prostoru konektoru zdroje do úložiště metaverse a cíl prostoru konektoru.

### <a name="changing-the-ad-ds-account-password"></a>Změna hesla účtu služby AD DS
Pokud změníte heslo účtu, synchronizační služba již nebude moci importovat/exportovat změny do místní AD.   Situace může vypadat takto:

- V kroku import/export pro službu AD connector selže s chybou "bez zahájení pověření".
- V prohlížeči událostí pro Windows protokolu událostí aplikace obsahuje chybu 6000 ID události, zpráva "agenta pro správu"contoso.com"se nepodařilo spustit, protože pověření je neplatné."

Chcete-li vyřešit tento problém, aktualizujte uživatelský účet služby AD DS, následujícím způsobem:


1. Spusťte Synchronization Service Manager (Služba synchronizace → START).
</br>![Správce synchronizace služby](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Přejděte **konektory** kartu.
3. Vyberte konektor AD, který je konfigurován pro použití účtu služby AD DS.
4. V části Akce, vyberte **vlastnosti**.
5. V místním dialogovém okně vyberte připojit k doménové struktuře služby Active Directory:
6. Určuje název doménové struktury odpovídající místní AD.
7. Uživatelské jméno označuje účet služby AD DS, který je používán k synchronizaci.
8. Zadejte nové heslo účtu služby AD DS do textového pole hesla ![Azure AD Connect Sync šifrování klíč nástroje](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Kliknutím na tlačítko OK uložte nové heslo a restartujte synchronizační službu pro odebrání předchozího hesla z mezipaměti.



## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
