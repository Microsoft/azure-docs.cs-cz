---
title: 'Azure AD Connect: Co je modul PowerShell ADConnectivityTool | Dokumenty společnosti Microsoft'
description: Tento dokument představuje nový modul ADConnectivity PowerShell a jak jej lze použít k řešení potíží.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64571116"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Poradce při potížích s připojením Azure AD S modulem PowerShell ADConnectivityTool

Nástroj ADConnectivity je modul prostředí PowerShell, který se používá v jedné z následujících možností:

- Během instalace, kdy problém s připojením k síti zabrání úspěšnému ověření pověření služby Active Directory, které uživatel poskytl v průvodci.
- Zaúčtovat instalaci uživatelem, který volá funkce z relace prostředí PowerShell.

Nástroj je umístěn v: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool během instalace

Na stránce **Připojit adresáře** v Průvodci připojením Azure AD, pokud dojde k problému se sítí, nástroj ADConnectivityTool automaticky použije jednu ze svých funkcí k určení, co se děje.  Za problémy se sítí lze považovat některou z následujících možností:

- Název doménové struktury, kterou uživatel zadával, byl zadán nesprávně nebo řekl, že doménová struktura neexistuje. 
- Port UDP 389 je uzavřen v řadičích domény přidružených k doménové struktuře, kterou uživatel
- Pověření uvedená v okně Účet doménové struktury služby AD nemají oprávnění k načtení řadičů domény přidružených k cílové doménové struktuře.
- Všechny porty TCP 53, 88 nebo 389 jsou uzavřeny v řadičích domény přidružených k doménové struktuře, kterou uživatel poskytl 
- UDP 389 i port TCP (nebo porty) jsou uzavřeny
- Službu DNS nelze přeložit pro zajišťovnu Doménovou strukturu a\nebo přidružené řadiče domény.

Kdykoli jsou některé z těchto problémů nalezeny, zobrazí se v Průvodci připojením AADConnect související chybová zpráva:


![Chyba](media/how-to-connect-adconnectivitytools/error1.png)

Například když se pokoušíme přidat adresář na obrazovce **Připojit adresáře,** Azure AD Connect potřebuje ověřit a očekává, že bude moci komunikovat s řadičem domény přes port 389.  Pokud to není možné, uvidíme chybu, která je zobrazena na obrázku výše.  

Co se ve skutečnosti děje na pozadí, je, `Start-NetworkConnectivityDiagnosisTools` že Azure AD Connect volá funkci.  Tato funkce je volána při selhání ověření pověření z důvodu problému s připojením k síti.

Nakonec je při každém volání nástroje z průvodce vygenerován podrobný soubor protokolu. Protokol je umístěn v **\<c:\ProgramData\AADConnect\ADConnectivityTool-\<datum>- čas>.log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools po instalaci
Po instalaci služby Azure AD Connect lze použít všechny funkce v modulu ADConnectivityTools PowerShell.  

Referenční informace o funkcích naleznete v referenční příručce [ADConnectivityTools](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Ověřování start-connectivityValidation

Budeme volat tuto funkci, protože to může být volána **pouze** ručně po ADConnectivityTool.psm1 byl importován do prostředí PowerShell. 

Tato funkce spustí stejnou logiku, kterou spustí Průvodce připojením Azure AD k ověření poskytnutých pověření služby AD.  Nicméně poskytuje mnohem podrobnější vysvětlení o problému a navrhované řešení. 

Ověření připojení se skládá z následujících kroků:
-   Získat objekt Doménfon DN (plně kvalifikovaný název domény)
-   Pokud uživatel vybral možnost Vytvořit nový účet služby AD, tato pověření patří do skupiny Enterprise Administrators.
-   Získat objekt FQDN doménové struktury
-   Zkontrolujte, zda je k dosažení alespoň jedné domény přidružené k dříve získanému objektu doménového struktury domény domény.
-   Ověřte, zda je funkční úroveň doménové struktury systému Windows Server 2003 nebo vyšší.

Uživatel bude moci přidat adresář, pokud byly všechny tyto akce úspěšně provedeny.

Pokud uživatel spustí tuto funkci po vyřešení problému (nebo pokud žádný problém neexistuje), výstup bude znamenat pro uživatele vrátit se k Průvodci připojením Azure AD a zkuste znovu vložit pověření.



## <a name="next-steps"></a>Další kroky
- [Azure AD Connect: Účty a oprávnění](reference-connect-accounts-permissions.md)
- [Expresní instalace](how-to-connect-install-express.md)
- [Vlastní instalace](how-to-connect-install-custom.md)
- [Referenční informace k ADConnectivityTools](reference-connect-adconnectivitytools.md)

