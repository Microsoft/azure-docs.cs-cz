---
title: 'Azure AD Connect: co je modul ADConnectivityTool PowerShellu | Microsoft Docs'
description: Tento dokument zavádí nový modul ADConnectivity PowerShellu a jeho použití k řešení potíží.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eed3e06ab42671d9674ad3893a88dfe9817e22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95973452"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Řešení potíží s připojením služby Azure AD pomocí modulu PowerShellu pro ADConnectivityTool

Nástroj ADConnectivity je modul prostředí PowerShell, který se používá v jednom z následujících postupů:

- Během instalace v případě, že potíže s připojením k síti brání úspěšnému ověření přihlašovacích údajů služby Active Directory, které zadal uživatel v průvodci.
- Po instalaci provede uživatel, který volá funkce z relace PowerShellu.

Nástroj se nachází v adresáři: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool. psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool během instalace

Pokud dojde k potížím se sítí, bude v průvodci Azure AD Connect na stránce **připojit adresáře** automaticky použita jedna z jeho funkcí k určení toho, co se děje.  Některé z následujících možností lze považovat za problémy se sítí:

- Název doménové struktury, kterou zadal uživatel, byl nesprávně zadán nebo tato doménová struktura neexistuje. 
- Port UDP 389 je uzavřený v řadičích domény přidružených k doménové struktuře, kterou zadal uživatel.
- Přihlašovací údaje zadané v okně účet doménové struktury služby Active Directory nemají oprávnění k načtení řadičů domény přidružených k cílové doménové struktuře.
- Všechny porty TCP 53, 88 nebo 389 se v řadičích domény přidružených k doménové struktuře, které zadal uživatel, zavřou. 
- Je uzavřený port UDP 389 i port TCP (nebo porty).
- Pro zadanou doménovou strukturu se nepovedlo vyřešit DNS, and\or přidružené řadiče domény.

Pokaždé, když se najde některé z těchto problémů, v průvodci AADConnect se zobrazí související chybová zpráva:


![Chyba](media/how-to-connect-adconnectivitytools/error1.png)

Například při pokusu o přidání adresáře na obrazovku **připojit adresáře** Azure AD Connect nutné tuto kontrolu ověřit a očekává, že bude moci komunikovat s řadičem domény přes port 389.  Pokud se to nepodaří, zobrazí se chyba zobrazená na snímku obrazovky výše.  

Co se skutečně děje na pozadí, je Azure AD Connect volání `Start-NetworkConnectivityDiagnosisTools` funkce.  Tato funkce se volá, když se ověření přihlašovacích údajů nepovede kvůli problému s připojením k síti.

Nakonec se při každém volání nástroje z Průvodce vygeneruje podrobný soubor protokolu. Protokol je umístěný v **C:\ProgramData\AADConnect\ADConnectivityTool- \<date> - \<time> . log.**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools po instalaci
Po instalaci Azure AD Connect můžete použít jakoukoli funkci v modulu PowerShellu ADConnectivityTools.  

Referenční informace o funkcích najdete v [referenčních](reference-connect-adconnectivitytools.md) informacích k ADConnectivityTools.

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Tuto funkci vyvoláme, protože ji můžete volat **jenom** ručně, až ADConnectivityTool. psm1 naimportujete do PowerShellu. 

Tato funkce provádí stejnou logiku, jakou spustí Průvodce Azure AD Connect pro ověření zadaných přihlašovacích údajů služby AD.  Poskytuje ale mnohem přesnější vysvětlení problému a navrhovaného řešení. 

Ověřování připojení se skládá z následujících kroků:
-   Získat objekt FQDN domény (plně kvalifikovaný název domény)
-   Ověří, jestli Pokud uživatel vybral možnost vytvořit nový účet služby AD, tyto přihlašovací údaje patří do skupiny Enterprise Administrators.
-   Získat objekt plně kvalifikovaného názvu domény doménové struktury
-   Ověřte, zda je dosažitelná alespoň jedna doména přidružená k dříve získanému objektu FQDN doménové struktury.
-   Ověřte, zda je funkční úroveň doménové struktury Windows Server 2003 nebo vyšší.

Uživatel bude moct přidat adresář, pokud se všechny tyto akce úspěšně provedly.

Pokud uživatel tuto funkci spustí po vyřešení problému (nebo pokud žádný problém vůbec neexistuje), bude výstup indikovat, že uživatel přejde zpět do Průvodce Azure AD Connect a pokusí se znovu vložit přihlašovací údaje.



## <a name="next-steps"></a>Další kroky
- [Azure AD Connect: Účty a oprávnění](reference-connect-accounts-permissions.md)
- [Expresní instalace](how-to-connect-install-express.md)
- [Vlastní instalace](how-to-connect-install-custom.md)
- [Referenční informace k ADConnectivityTools](reference-connect-adconnectivitytools.md)

