---
title: Instalace služby Azure AD Connect pomocí oprávnění delegovaného správce SQL | Dokumenty společnosti Microsoft
description: Toto téma popisuje aktualizaci Služby Azure AD Connect, která umožňuje instalaci pomocí účtu, který má pouze oprávnění SQL dbo.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60243515"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalace nástroje Azure AD Connect pomocí oprávnění delegovaného správce SQL
Před nejnovější sestavení Azure AD Connect, delegování správy při nasazování konfigurací, které vyžadují SQL, nebyl ampo-mailem.  Uživatelé, kteří chtěli nainstalovat Azure AD Connect potřebné mít oprávnění správce serveru (SA) na serveru SQL.

S nejnovější verzí Azure AD Connect, zřizování databáze lze nyní provádět mimo pásmo správcem SQL a pak nainstalovat správce Azure AD Connect s právy vlastníka databáze.

## <a name="before-you-begin"></a>Než začnete
Chcete-li tuto funkci použít, musíte si uvědomit, že existuje několik pohyblivých částí a každá z nich může zahrnovat jiného správce ve vaší organizaci.  Následující tabulka shrnuje jednotlivé role a jejich příslušné povinnosti při nasazování Azure AD Connect s touto funkcí.

|Role|Popis|
|-----|-----|
|Správce služby AD domény nebo doménové struktury|Vytvoří účet služby na úrovni domény, který používá Azure AD Connect ke spuštění synchronizační služby.  Další informace o účtech služeb naleznete v [tématu Účty a oprávnění](reference-connect-accounts-permissions.md).
|Správce SQL|Vytvoří databázi ADSync a uděluje přihlášení + dbo přístup k azure ad připojit správce a účet služby vytvořené domény nebo doménové struktury správce.|
Správce služby Azure AD Connect|Nainstaluje Azure AD Connect a určuje účet služby během vlastní instalace.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Postup instalace Azure AD Connect pomocí delegovaných oprávnění SQL
Chcete-li zřídit databázi mimo pásmo a nainstalovat Azure AD Connect s oprávněními vlastníka databáze, použijte následující kroky.

>[!NOTE]
>I když to není nutné, důrazně **doporučujeme, aby** Latin1_General_CI_AS řazení je vybrána při vytváření databáze.


1. Chcete, aby správce SQL vytvořil databázi ADSync s posloupností řazení bez rozlišování velkých a malých písmen **(Latin1_General_CI_AS).**  Databáze musí mít název **ADSync**.  Model obnovení, úroveň kompatibility a typ uzavření jsou aktualizovány na správné hodnoty při instalaci služby Azure AD Connect.  Pořadí řazení však musí být správně nastaveno správcem SQL, jinak Azure AD Connect zablokuje instalaci.  Chcete-li obnovit sa musí odstranit a znovu vytvořit databázi.
 
   ![Kolace](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Udělte správci Služby Azure AD Connect a účtu služby domény následující oprávnění:
   - Přihlášení k SQL serveru 
   - práva **vlastníka (dbo)** databáze.
 
   ![Oprávnění](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect nepodporuje přihlášení s vnořeným členstvím.  To znamená, že váš účet správce Azure AD Connect a účet služby domény musí být propojeny s přihlášením, které je uděleno dbo práva.  Nemůže být jednoduše členem skupiny, která je přiřazena k přihlášení s dbo právy.

3. Odešlete e-mail správci Azure AD Connect s uvedením názvu serveru SQL a instance, který se má použít při instalaci služby Azure AD Connect.

## <a name="additional-information"></a>Další informace
Po zřízení databáze může správce Služby Azure AD Connect nainstalovat a nakonfigurovat místní synchronizaci podle jejich pohodlí.

V případě, že správce SQL obnovil databázi ADSync z předchozí zálohy Azure AD Connect, budete muset nainstalovat nový server Azure AD Connect pomocí existující databáze. Další informace o instalaci Služby Azure AD Connect s existující databází najdete v [tématu Instalace služby Azure AD Connect pomocí existující databáze ADSync](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Další kroky
- [Začínáme se službou Azure AD Connect s použitím expresního nastavení](how-to-connect-install-express.md)
- [Vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md)
- [Instalace nástroje Azure AD Connect s využitím existující databáze ADSync](how-to-connect-install-existing-database.md)  
