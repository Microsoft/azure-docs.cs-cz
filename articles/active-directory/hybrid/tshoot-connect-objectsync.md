---
title: 'Azure AD Connect: řešení potíží se synchronizací objektů | Microsoft Docs'
description: V tomto tématu najdete postup řešení potíží se synchronizací objektů pomocí úlohy řešení potíží.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d4239dd34f2a64aa7b3edbf88bad4348e01291
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85356198"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Řešení potíží se synchronizací objektů pomocí synchronizace Azure AD Connect
Tento článek popisuje kroky pro řešení potíží se synchronizací objektů pomocí úlohy řešení potíží. Pokud chcete zjistit, jak řešení potíží funguje ve službě Azure Active Directory (Azure AD), podívejte se na [Toto krátké video](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Úloha řešení potíží
V případě nasazení Azure AD Connect s verzí 1.1.749.0 nebo novější k řešení potíží se synchronizací objektů použijte úlohu řešení potíží v průvodci. V případě starších verzí je třeba řešit potíže ručně, jak je popsáno [zde](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Spuštění úlohy řešení potíží v průvodci
Chcete-li spustit úlohu řešení potíží v průvodci, proveďte následující kroky:

1.  Na serveru Azure AD Connect otevřete novou relaci prostředí Windows PowerShell s možností spustit jako správce.
2.  Spusťte `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted` .
3.  Spusťte Průvodce Azure AD Connect.
4.  Přejděte na stránku další úlohy, vyberte možnost řešení potíží a klikněte na tlačítko Další.
5.  Na stránce Poradce při potížích klikněte na spustit a spusťte v PowerShellu nabídku pro odstraňování potíží.
6.  V hlavní nabídce vyberte řešení potíží s synchronizací objektů.
![Řešení potíží se synchronizací objektů](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Řešení potíží se vstupními parametry
Úkol odstraňování potíží vyžaduje následující vstupní parametry:
1.  **Rozlišující název objektu** – jedná se o rozlišující název objektu, který vyžaduje řešení potíží.
2.  **Název konektoru služby AD** – jedná se o název doménové struktury služby Active Directory, ve které se nachází výše uvedený objekt.
3.  Přihlašovací údaje globálního správce tenanta služby Azure AD pro globální správce ![](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Vysvětlení výsledků úlohy odstraňování potíží
Úloha řešení potíží provádí následující kontroly:

1.  Rozpoznat neshodu hlavního názvu uživatele, pokud je objekt synchronizovaný, aby Azure Active Directory
2.  Ověřit, zda je objekt filtrován z důvodu filtrování domény
3.  Ověřit, zda je objekt filtrován z důvodu filtrování organizační jednotky
4.  Zkontroluje, jestli je synchronizace objektů zablokovaná kvůli propojené poštovní schránce.
5. Zkontroluje, jestli je objekt dynamická distribuční skupina, která by neměla být synchronizovaná.

Zbytek této části popisuje konkrétní výsledky, které jsou vráceny úlohou. V každém případě úkol poskytuje analýzu následovaný doporučenými akcemi pro vyřešení tohoto problému.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Detekovat neshodu hlavního názvu uživatele, pokud je objekt synchronizovaný na Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Přípona UPN se neověřuje u tenanta Azure AD.
Když přípona ID přihlášení/Alternate není ověřená u tenanta Azure AD, pak Azure Active Directory nahradí přípony hlavního názvu uživatele (UPN) výchozím názvem domény "onmicrosoft.com".

![Azure AD nahrazuje hlavní název uživatele (UPN)](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Funkce SynchronizeUpnForManagedUsers tenanta Azure AD DirSync je zakázaná.
Pokud je DirSync funkce SynchronizeUpnForManagedUsers tenanta Azure AD zakázaná, Azure Active Directory nepovoluje aktualizace synchronizace pro: UserPrincipalName/alternativní přihlašovací ID pro licencované uživatelské účty se spravovaným ověřováním.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objekt je filtrován z důvodu filtrování domény.
### <a name="domain-is-not-configured-to-sync"></a>Doména není nakonfigurovaná na synchronizaci.
Objekt je mimo rozsah z důvodu nekonfigurace domény. V následujícím příkladu je objekt mimo rozsah synchronizace, protože doména, do které patří, je filtrována z synchronizace.

![Doména není nakonfigurovaná na synchronizaci.](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Doména je nakonfigurovaná pro synchronizaci, ale chybí profily/kroky spuštění.
Objekt je mimo rozsah, protože v doméně chybí profil spuštění/kroky spuštění. V následujícím příkladu je objekt mimo rozsah synchronizace, protože v doméně, do které patří, chybí kroky spuštění pro úplný profil spuštění importu.
![chybějící profily spuštění](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Objekt je filtrován z důvodu filtrování organizační jednotky.
Objekt je mimo rozsah synchronizace z důvodu konfigurace filtrování organizační jednotky. V následujícím příkladu objekt patří do OU = inSync, DC = bvtadwbackdc, DC = com.  Tato organizační jednotka není zahrnutá v oboru synchronizace.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problémy s propojenou poštovní schránkou
Propojená poštovní schránka by měla být přidružená k externímu hlavnímu účtu umístěnému v jiné doménové struktuře důvěryhodných účtů. Pokud žádný takový externí hlavní účet neexistuje, Azure AD Connect nebude synchronizovat uživatelský účet, který odpovídá propojené poštovní schránce v doménové struktuře Exchange pro tenanta Azure AD.</br>
![Propojená poštovní schránka](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problém s dynamickou distribuční skupinou
Z důvodu různých rozdílů mezi místními službami Active Directory a Azure Active Directory Azure AD Connect nesynchronizuje dynamické distribuční skupiny s klientem služby Azure AD.

![Dynamická distribuční skupina](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Sestava HTML
Kromě analýzy objektu vygeneruje úloha odstraňování potíží také sestavu jazyka HTML, která má vše známou o objektu. Tuto sestavu HTML můžete sdílet s týmem podpory, aby bylo možné v případě potřeby dále řešit problémy.

![Sestava HTML](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
