---
title: Konektory v uživatelském rozhraní Synchronization Service Manager Azure AD | Microsoft Docs
description: Pochopte kartu konektory v Synchronization Service Manager pro Azure AD Connect.
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
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319873"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Používání konektorů s Azure AD Connect synchronizace Service Manager

![Snímek obrazovky zobrazující Service Manager synchronizace](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Karta konektory slouží ke správě všech systémů, ke kterým je synchronizační modul připojen.

## <a name="connector-actions"></a>Akce konektoru
| Akce | Komentář |
| --- | --- |
| Vytvořit |Nepoužívat. Pro připojení k dalším doménovým strukturám služby AD použijte Průvodce instalací nástroje. |
| Vlastnosti |Používá se pro filtrování domén a organizačních jednotek. |
| [Odstranit](#delete) |Slouží k odstranění dat v prostoru konektoru nebo k odstranění připojení k doménové struktuře. |
| [Konfigurovat profily spuštění](#configure-run-profiles) |S výjimkou filtrování domén není tady nic konfigurovat. Tuto akci můžete použít k zobrazení již nakonfigurovaných profilů spuštění. |
| Spustit |Slouží ke spuštění jednorázového spuštění profilu. |
| Zastavit |Zastaví konektor, který aktuálně používá profil. |
| Exportovat konektor |Nepoužívat. |
| Import konektoru |Nepoužívat. |
| Aktualizovat konektor |Nepoužívat. |
| Aktualizovat schéma |Aktualizuje schéma v mezipaměti. Místo toho je vhodnější použít možnost v Průvodci instalací, protože tato aktualizace také aktualizuje pravidla synchronizace. |
| [Hledat místo v konektoru](#search-connector-space) |Slouží k hledání objektů a ke sledování objektu a jeho dat prostřednictvím systému. |

### <a name="delete"></a>Odstranit
Akce Odstranit se používá pro dvě různé věci.  
![Snímek obrazovky, který zobrazuje okno Odstranit spojnici s vybranou možnost odstranit pouze prostor konektoru](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Možnost **Odstranit místo konektoru odebere jenom** všechna data, ale ponechá konfiguraci.

Možnost **Odstranit spojnici a spojovací prostor** odstraní data a konfiguraci. Tato možnost se používá, když se už nechcete připojit k doménové struktuře.

Obě možnosti synchronizují všechny objekty a aktualizují objekty Metaverse. Tato akce je dlouhodobě spuštěná operace.

### <a name="configure-run-profiles"></a>Konfigurovat profily spuštění
Tato možnost umožňuje zobrazit profily spuštění nakonfigurované pro konektor.

![Snímek obrazovky, který zobrazuje okno Konfigurovat profily spuštění s vybraným rozdílovým importem](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Hledat místo v konektoru
Akce místa konektoru hledání je užitečná pro hledání objektů a řešení problémů s daty.

![Snímek obrazovky, který zobrazuje okno "prostor pro hledání konektoru".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Začněte výběrem **oboru**. Můžete vyhledávat na základě dat (RDN, DN, kotvy, podstromy) nebo stavu objektu (všechny ostatní možnosti).  
![Snímek obrazovky, který zobrazuje rozevírací nabídku "obor".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Pokud například provedete dílčí stromové hledání, získáte všechny objekty v jedné organizační jednotce.  
![Snímek obrazovky, který zobrazuje příklad hledání "sub-Tree".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Z této mřížky můžete vybrat objekt, vybrat **vlastnosti**a [sledovat ho](tshoot-connect-object-not-syncing.md) z prostoru zdrojového konektoru, do úložiště metaverse a do cílového prostoru konektoru.

### <a name="changing-the-ad-ds-account-password"></a>Změna hesla účtu služby AD DS
Pokud změníte heslo účtu, synchronizační služba už nebude moct importovat a exportovat změny v místní službě AD.   Situace může vypadat takto:

- V kroku import/export pro konektor AD dojde k chybě s chybou "bez spuštění-přihlašovacích údajů".
- V části Windows Prohlížeč událostí obsahuje protokol událostí aplikace chybu s ID události 6000 a zprávou "Agent pro správu" contoso.com "se nepodařilo spustit, protože přihlašovací údaje byly neplatné."

Chcete-li tento problém vyřešit, aktualizujte služba AD DS uživatelský účet pomocí následujících kroků:


1. Spusťte Synchronization Service Manager (spusťte synchronizační službu →).
</br>![Synchronizovat Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Přejít na kartu **konektory** .
3. Vyberte konektor služby AD, který je nakonfigurovaný tak, aby používal účet služba AD DS.
4. V části Akce vyberte **vlastnosti**.
5. V místním dialogovém okně vyberte připojit k doménové struktuře služby Active Directory:
6. Název doménové struktury označuje odpovídající místní službu AD.
7. Uživatelské jméno indikuje služba AD DS účet, který se používá k synchronizaci.
8. Zadejte nové heslo účtu služba AD DS v textovém poli hesla ![ Azure AD Connect Nástroj pro synchronizaci šifrovacího klíče.](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Kliknutím na tlačítko OK uložte nové heslo a restartujte synchronizační službu, aby se odebralo staré heslo z mezipaměti paměti.



## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md) .

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
