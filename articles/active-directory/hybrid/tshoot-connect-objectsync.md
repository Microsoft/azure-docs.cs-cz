---
title: 'Azure AD Connect: Poradce při potížích se synchronizací objektů | Dokumenty společnosti Microsoft'
description: Toto téma obsahuje postup řešení problémů se synchronizací objektů pomocí úlohy řešení potíží.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e10d00ed90248319801974c7c1e7fadf835024b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407020"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Řešení potíží se synchronizací objektů pomocí synchronizace Azure AD Connect
Tento článek obsahuje postup řešení potíží se synchronizací objektů pomocí úlohy řešení potíží. Chcete-li zjistit, jak řešení potíží funguje ve službě Azure Active Directory (Azure AD) Connect, podívejte se na [toto krátké video](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Úloha řešení potíží
V případě nasazení Azure AD Connect s verzí 1.1.749.0 nebo novější k řešení potíží se synchronizací objektů použijte úlohu řešení potíží v průvodci. U dřívějších verzí vyřešujte potíže ručně, jak je popsáno [zde](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Spuštění úlohy řešení potíží v průvodci
Chcete-li spustit úlohu řešení potíží v průvodci, proveďte následující kroky:

1.  Otevřete novou relaci prostředí Windows PowerShell na serveru Azure AD Connect s možností Spustit jako správce.
2.  Spustit `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`nebo .
3.  Spusťte Průvodce připojením Azure AD Connect.
4.  Přejděte na stránku Další úkoly, vyberte Poradce při potížích a klikněte na Další.
5.  Na stránce Poradce při potížích klikněte na Spustit a spusťte nabídku řešení potíží v PowerShellu.
6.  V hlavní nabídce vyberte Poradce při potížích se synchronizací objektů.
![Poradce při potížích se synchronizací objektů](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Poradce při potížích se vstupními parametry
Úloha řešení potíží potřebuje následující vstupní parametry:
1.  **Rozlišující název objektu** – toto je rozlišující název objektu, který potřebuje řešení potíží
2.  **Název konektoru služby AD** – toto je název doménové struktury služby AD, kde se nachází výše uvedený objekt.
3.  Pověření globálního správce klienta ![Azure AD pověření globálního správce](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Vysvětlení výsledků úlohy řešení potíží
Úloha řešení potíží provádí následující kontroly:

1.  Zjištění neshody hlavního upn, pokud je objekt synchronizován se službou Azure Active Directory
2.  Kontrola filtrování objektu z důvodu filtrování domény
3.  Kontrola, zda je objekt filtrován z důvodu filtrování ou.
4.  Kontrola, zda je synchronizace objektů blokována z důvodu propojené poštovní schránky
5. Zkontrolujte, zda je objekt dynamickou distribuční skupinou, která nemá být synchronizována.

Zbývající část této části popisuje konkrétní výsledky, které jsou vráceny úkolem. V každém případě úkol poskytuje analýzu následovanou doporučenými akcemi k vyřešení problému.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Zjištění neshody upn při synchronizaci objektu do služby Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Přípona UPN není ověřena pomocí klienta Azure AD
Když přípona UserPrincipalName (UPN)/Alternate Login ID není ověřena pomocí tenanta Azure AD, azure active directory nahradí přípony UPN s výchozím názvem domény "onmicrosoft.com".

![Azure AD nahradí UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Funkce DirSync klienta Azure AD Je zakázána
Když je funkce DirSync klienta Azure AD "SynchronizeUpnForManagedUsers" zakázána, služba Azure Active Directory nepovoluje synchronizaci aktualizací uživatelského jména/alternativního přihlašovacího ID pro licencované uživatelské účty se spravovaným ověřováním.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objekt je filtrován z důvodu filtrování domény.
### <a name="domain-is-not-configured-to-sync"></a>Doména není nakonfigurována pro synchronizaci.
Objekt je mimo rozsah z důvodu domény není nakonfigurován. V níže uvedeném příkladu je objekt mimo rozsah synchronizace, protože doména, do které patří, je filtrována ze synchronizace.

![Doména není nakonfigurována pro synchronizaci.](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Doména je nakonfigurována pro synchronizaci, ale chybí profily spuštění nebo kroky spuštění
Objekt je mimo rozsah, protože v doméně chybí spustit profily nebo kroky spuštění. V následujícím příkladu je objekt mimo rozsah synchronizace, protože doména, do které patří, chybí kroky spuštění pro profil spuštění úplného importu.
![chybějící profily spuštění](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Objekt je filtrován z důvodu filtrování ou.
Objekt je mimo rozsah synchronizace z důvodu konfigurace filtrování ou. V níže uvedeném příkladu objekt patří do OU= NoSync,DC =bvtadwbackdc,DC=com.  Tato ouonina není součástí oboru synchronizace.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problém s propojenou poštovní schránkou
Propojená poštovní schránka má být přidružena k externímu hlavnímu účtu umístěnému v jiné doménové struktuře důvěryhodného účtu. Pokud takový externí hlavní účet neexistuje, azure ad connect nebude synchronizovat uživatelský účet odpovídá propojené poštovní schránce v doménové struktuře Exchange do tenanta Azure AD.</br>
![Propojená poštovní schránka](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problém s dynamickou distribuční skupinou
Vzhledem k různým rozdílům mezi místní službou Active Directory a službou Azure Active Directory azure ad connect nesynchronizuje dynamické distribuční skupiny s klientem Azure AD.

![Dynamická distribuční skupina](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Sestava HTML
Kromě analýzy objektu úloha řešení potíží také generuje sestavu HTML, která má vše, co je o objektu známo. Tuto sestavu HTML lze v případě potřeby sdílet s týmem podpory za účelem dalšího řešení potíží.

![Sestava HTML](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
