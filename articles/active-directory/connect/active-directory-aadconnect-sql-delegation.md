---
title: Nainstalujte Azure AD Connect pomocí SQL delegovaného správce oprávnění | Microsoft Docs
description: Toto téma popisuje aktualizaci na Azure AD Connect, který umožňuje instalaci pomocí účtu, který má pouze oprávnění SQL dbo.
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: d0cfce1dfe6482397e421d85d59494e6d75cc441
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591153"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalace služby Azure AD Connect s použitím SQL delegovaná oprávnění správce
Před posledním sestavení Azure AD Connect správu delegování, při nasazování konfigurace, které vyžaduje SQL, nepodporuje.  Uživatelé, kteří chtěli nainstalovat Azure AD Connect je potřeba mít oprávnění správce (SA) serveru na serveru SQL server.

V nejnovější verzi služby Azure AD Connect se teď zřizování databáze může být vzdáleně provádí správce SQL a poté se nainstaluje Správce Azure AD Connect s práva vlastník databáze.

## <a name="before-you-begin"></a>Než začnete
Chcete-li tuto funkci používat, uvědomte si, že existuje několik částí přesunutí a každé z nich může zahrnovat jiného správce ve vaší organizaci.  Následující tabulka shrnuje jednotlivých rolí a jejich povinností v nasazení Azure AD Connect s touto funkcí.

|Role|Popis|
|-----|-----|
|Domény nebo doménové struktuře AD správce|Vytvoří účet domény úrovně služby, který slouží ke spuštění synchronizační služby Azure AD Connect.  Další informace o účtech služby najdete v tématu [účty a oprávnění](active-directory-aadconnect-accounts-permissions.md).
|Správce SQL|Vytvoří databáze ADSync a přihlášení + dbo uděluje přístup k Azure AD Connect správce a účet služby vytvořené správce domény nebo doménové struktuře.|
Správce služby Azure AD Connect|Nainstaluje Azure AD Connect a určuje účet služby během vlastní instalace.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Kroky pro instalaci Azure AD Connect pomocí SQL delegovaná oprávnění
K poskytnutí databáze vzdálené správy a nainstalujte Azure AD Connect s oprávnění vlastníka databáze, použijte následující postup.

>[!NOTE]
>I když to není nutné, je **důrazně doporučujeme** , Latin1_General_CI_AS kolace je vybraná při vytváření databáze.


1.  Požádejte správce SQL vytvořit databázi ADSync s pořadí písmen kolace **(Latin1_General_CI_AS)**.  Databáze musí mít název **ADSync**.  Model obnovení, úroveň kompatibility a typ členství ve skupině jsou aktualizovány na správné hodnoty při instalaci Azure AD Connect.  Ale pořadí kolace musí být správně nastavené správcem SQL Azure AD Connect bude blokoval instalace.  K obnovení přidružení zabezpečení potřeba odstranit a znovu vytvořit databázi.</br>
![Kolace](media/active-directory-aadconnect-sql-delegation/sql4.png)
2.  Přidělte správce Azure AD Connect a účet služby domény následující oprávnění:
    - Přihlášení k SQL serveru 
    - **databáze owner(dbo)** práva.  </br>
![Oprávnění](media/active-directory-aadconnect-sql-delegation/sql3.png)
3.  Odešlete e-mail na správce Azure AD Connect, která udává název SQL serveru a instance, který se má použít při instalaci Azure AD Connect.

## <a name="additional-information"></a>Další informace
Po zřízení databáze, správce Azure AD Connect můžete nainstalovat a nakonfigurovat místní synchronizace v jejich pohodlí.  

**/UseExistingDatabase** příznak je potřeba při použití předem vytvořené databáze.  Jenom se nepoužívá v situacích, obnovení.

Také podporuje nové instalace služby Azure AD Connect, tato funkce také umožňuje delegování pro každý scénář související s **/UseExistingDatabase** příznak.  Další informace o instalaci Azure AD Connect s existující databázi, naleznete v části [použití existující databáze ADSync instalaci Azure AD Connect](active-directory-aadconnect-existing-database.md)


## <a name="next-steps"></a>Další postup
- [Začínáme se službou Azure AD Connect s použitím expresního nastavení](active-directory-aadconnect-get-started-express.md)
- [Vlastní instalace služby Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
- [Nainstalujte Azure AD Connect použití existující databáze ADSync](active-directory-aadconnect-existing-database.md)  
