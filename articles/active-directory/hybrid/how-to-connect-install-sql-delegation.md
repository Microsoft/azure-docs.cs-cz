---
title: Instalace Azure AD Connect s použitím oprávnění delegovaného správce SQL | Microsoft Docs
description: Toto téma popisuje aktualizaci Azure AD Connect, která umožňuje instalaci pomocí účtu, který má pouze oprávnění SQL dbo.
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
ms.topic: how-to
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f082ec896bf0542b63c8c1d0257679681334050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85358663"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalace nástroje Azure AD Connect pomocí oprávnění delegovaného správce SQL
Před nejnovějším sestavením Azure AD Connect se delegování správců při nasazování konfigurací vyžadovaných SQL nepodporovalo.  Uživatelé, kteří chtěli nainstalovat Azure AD Connect nutné k tomu, aby měli oprávnění správce serveru (SA) na SQL serveru.

S nejnovějším vydáním Azure AD Connect může být zřízení databáze nyní provedeno správcem SQL a poté je nainstalováno správcem Azure AD Connect s právy k vlastnictví databáze.

## <a name="before-you-begin"></a>Než začnete
Pokud chcete používat tuto funkci, musíte si uvědomit, že existuje několik pohybujících se částí a každá z nich může zahrnovat jiného správce ve vaší organizaci.  Následující tabulka shrnuje jednotlivé role a jejich příslušné úkoly při nasazení Azure AD Connect s touto funkcí.

|Role|Popis|
|-----|-----|
|Správce služby AD domény nebo doménové struktury|Vytvoří účet služby na úrovni domény, který Azure AD Connect používá ke spuštění služby synchronizace.  Další informace o účtech služeb najdete v tématu [účty a oprávnění](reference-connect-accounts-permissions.md).
|Správce SQL|Vytvoří databázi ADSync a udělí správci Azure AD Connect a účtu služby vytvořenému správcem domény nebo doménové struktury oprávnění Login + dbo.|
Správce Azure AD Connect|Nainstaluje Azure AD Connect a určí účet služby při vlastní instalaci.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Postup instalace Azure AD Connect s použitím delegovaných oprávnění SQL
Pokud chcete databázi zřídit vzdáleně a nainstalovat Azure AD Connect s oprávněními vlastníka databáze, použijte následující postup.

>[!NOTE]
>I když to není nutné, **důrazně doporučujeme** , abyste při vytváření databáze zvolili Latin1_General_CI_AS kolaci.


1. Správce SQL vytvoří databázi ADSync s posloupností řazení bez rozlišení velkých a malých písmen **(Latin1_General_CI_AS)**.  Databáze musí mít název **AdSync**.  Model obnovení, úroveň kompatibility a typ omezení jsou aktualizovány na správné hodnoty při instalaci Azure AD Connect.  Je-li však nutné pořadí řazení nastavit správně správcem SQL, jinak Azure AD Connect zablokuje instalaci.  Chcete-li obnovit SA, musí databáze odstranit a znovu vytvořit.
 
   ![Kolace](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Udělte správci Azure AD Connect a účtu doménové služby následující oprávnění:
   - Přihlášení k SQL 
   - práva **vlastníka databáze (dbo)** .
 
   ![Oprávnění](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect nepodporuje přihlášení s vnořeným členstvím.  To znamená, že účet správce Azure AD Connect a účet služby domény musí být propojeny s přihlašovacími údaji, kterým je uděleno oprávnění dbo.  Nemůže být jednoduše členem skupiny, která je přiřazená k přihlašovacímu jménu s právy dbo.

3. Odeslání e-mailu správci Azure AD Connect, který označuje název SQL serveru a instance, který se má použít při instalaci Azure AD Connect

## <a name="additional-information"></a>Další informace
Po zřízení databáze může správce Azure AD Connect nainstalovat a nakonfigurovat místní synchronizaci na jejich pohodlí.

V případě, že správce SQL obnovil databázi ADSync z předchozí zálohy Azure AD Connect, budete muset nový server Azure AD Connect nainstalovat pomocí existující databáze. Další informace o instalaci Azure AD Connect s existující databází najdete v článku [instalace Azure AD Connect pomocí existující databáze AdSync](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Další kroky
- [Začínáme se službou Azure AD Connect s použitím expresního nastavení](how-to-connect-install-express.md)
- [Vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md)
- [Instalace nástroje Azure AD Connect s využitím existující databáze ADSync](how-to-connect-install-existing-database.md)  
