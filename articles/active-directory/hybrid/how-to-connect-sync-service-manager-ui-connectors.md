---
title: Konektory v uzd umulování synchronizace Azure AD | Dokumenty společnosti Microsoft
description: Seznamte se s kartou Konektory ve Správci synchronizačních služeb pro Azure AD Connect.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae932191c7b76590ea217386dfd729add5566f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261044"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Použití konektorů se Správcem synchronizačních služeb Azure AD Connect

![Správce synchronizačních služeb](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Karta Konektory slouží ke správě všech systémů, ke kterým je synchronizační modul připojen.

## <a name="connector-actions"></a>Akce konektoru
| Akce | Poznámka |
| --- | --- |
| Vytvořit |Nepoužívat. Pro připojení k dalším doménám služby AD použijte průvodce instalací. |
| Vlastnosti |Používá se pro filtrování domény a ou. |
| [Odstranit](#delete) |Slouží k odstranění dat v prostoru spojnice nebo k odstranění připojení k doménové struktuře. |
| [Konfigurace profilů spuštění](#configure-run-profiles) |Kromě filtrování domény zde není nic, co by bylo k onoho konfigurovat. Pomocí této akce můžete zobrazit již nakonfigurované profily spuštění. |
| Spusťte |Používá se k zahájení jednorázového spuštění profilu. |
| Zastavit |Zastaví konektor, který aktuálně spouštějí profil. |
| Exportní konektor |Nepoužívat. |
| Importovat konektor |Nepoužívat. |
| Aktualizovat konektor |Nepoužívat. |
| Aktualizovat schéma |Aktualizuje schéma uložené v mezipaměti. Místo toho se doporučuje použít tuto možnost v průvodci instalací, protože to také aktualizuje pravidla synchronizace. |
| [Hledat mezeru v spojnici](#search-connector-space) |Slouží k hledání objektů a sledování objektu a jeho dat prostřednictvím systému. |

### <a name="delete"></a>Odstranění
Akce odstranění se používá pro dvě různé věci.  
![Správce synchronizačních služeb](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Možnost **Odstranit místo konektoru pouze** odebere všechna data, ale zachová konfiguraci.

Možnost **Odstranit konektor a prostor konektoru** odebere data a konfiguraci. Tato možnost se používá, pokud se již nechcete připojit k doménové struktuře.

Obě možnosti synchronizují všechny objekty a aktualizují metaverse objekty. Tato akce je dlouho běžící operace.

### <a name="configure-run-profiles"></a>Konfigurace profilů spuštění
Tato možnost umožňuje zobrazit profily spuštění nakonfigurované pro konektor.

![Správce synchronizačních služeb](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Hledat mezeru v spojnici
Akce prostoru vyhledávacího konektoru je užitečná pro vyhledání objektů a řešení problémů s daty.

![Správce synchronizačních služeb](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Začněte výběrem **oboru**. Můžete vyhledávat na základě dat (RDN, DN, Anchor, Sub-Tree) nebo stavu objektu (všechny ostatní možnosti).  
![Správce synchronizačních služeb](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Pokud například projdete vyhledávání maří, získáte všechny objekty v jedné řadě.  
![Správce synchronizačních služeb](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Z této mřížky můžete vybrat objekt, vybrat **vlastnosti**a [sledovat ho](tshoot-connect-object-not-syncing.md) ze zdrojového prostoru spojnice, přes metaverse a do prostoru cílové spojnice.

### <a name="changing-the-ad-ds-account-password"></a>Změna hesla účtu služby AD DS
Pokud změníte heslo účtu, služba synchronizace již nebude moci importovat nebo exportovat změny do místní služby AD.   Situace může vypadat takto:

- Krok importu a exportu pro konektor služby AD se nezdaří s chybou "no-start-credentials".
- V prohlížeči událostí systému Windows obsahuje protokol událostí aplikace chybu s ID události 6000 a zprávu "Agent pro správu contoso.com" se nepodařilo spustit, protože pověření byla neplatná.

Chcete-li tento problém vyřešit, aktualizujte uživatelský účet služby AD DS následujícím:


1. Spusťte Správce synchronizačních služeb (START → Služba synchronizace).
</br>![Správce synchronizačních služeb](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Přejděte na kartu **Konektory.**
3. Vyberte konektor ad, který je nakonfigurován pro použití účtu ad ds.
4. V části Akce vyberte **Vlastnosti**.
5. V rozbalovacím dialogovém okně vyberte Připojit k doménové struktuře služby Active Directory:
6. Název doménové struktury označuje odpovídající místní službu AD.
7. Uživatelské jméno označuje účet ad DS používaný pro synchronizaci.
8. Zadejte nové heslo účtu služby AD DS do textového pole ![Heslo Nástroje pro šifrování šifrovacího klíče synchronizace připojení služby Azure AD Connect](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Klepnutím na tlačítko OK uložte nové heslo a restartujte službu synchronizace, chcete-li odebrat staré heslo z mezipaměti paměti.



## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [synchronizace Azure AD Connect.](how-to-connect-sync-whatis.md)

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
