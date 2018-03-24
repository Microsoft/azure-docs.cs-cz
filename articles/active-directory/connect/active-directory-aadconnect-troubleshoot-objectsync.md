---
title: 'Azure AD Connect: Objekt synchronizace řešení potíží s | Microsoft Docs'
description: Toto téma obsahuje postup pro řešení potíží se objekt synchronizace pomocí řešení potíží úlohy.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: billmath
ms.openlocfilehash: 54ae18b9a802fe078d307f4d36400adf806b233f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Řešení potíží s objekt synchronizace s synchronizace Azure AD Connect
Tento dokument obsahuje kroky pro řešení potíží s objekt synchronizace pomocí řešení potíží úlohy.

## <a name="troubleshooting-task"></a>Řešení potíží s úloh
Pro Azure Active Directory (AAD) připojit nasazení s verzí 1.1.749.0 nebo vyšší, použijte úlohu odstraňování potíží v Průvodci potíží objekt synchronizace. U starších verzí, prosím vyřešit ručně, jak je popsáno [zde](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Spusťte úlohu odstraňování potíží v Průvodci
Pokud chcete spustit úlohu řešení potíží v průvodci, proveďte následující kroky:

1.  Otevřete novou relaci prostředí Windows PowerShell na serveru Azure AD Connect s spustit jako správce.
2.  Spustit `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted`.
3.  Spusťte Průvodce službou Azure AD Connect.
4.  Přejděte na stránku další úlohy, vyberte Poradce při potížích a klikněte na tlačítko Další.
5.  Na stránce Poradce při potížích s kliknutím na tlačítko spustit Poradce při potížích nabídky start v prostředí PowerShell.
6.  V hlavní nabídce vyberte řešení objekt synchronizace.

### <a name="troubleshooting-input-parameters"></a>Řešení potíží s vstupní parametry
Řešení problémů s úlohou jsou potřeba následující vstupní parametry:
1.  **Rozlišující název objektu** – to je rozlišující název objektu, který potřebuje řešení potíží
2.  **Název konektoru AD** – to je název doménové struktuře AD, které se nachází objekt výše.
3.  Přihlašovací údaje globálního správce klienta Azure AD ![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Pochopení výsledky úlohy řešení potíží
Řešení potíží úloha provede následující kontroly:

1.  Zjištění neshoda UPN, pokud je objekt synchronizovat s Azure Active Directory
2.  Zkontrolujte, pokud je objekt filtrován kvůli filtrování domény
3.  Zkontrolujte, pokud objekt je filtrovaná kvůli filtrování organizační jednotky

Zbývající část tohoto oddílu popisuje konkrétní výsledky, které jsou vráceny úlohou. V každém případě úlohy obsahuje analýzu následuje doporučené akce k vyřešení problému.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Zjištění neshoda UPN, pokud je objekt synchronizovat s Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Přípona UPN je Neověřeno s klientem Azure AD
Když UserPrincipalName (UPN) / přípona alternativního přihlašovacího ID není ověření u klienta Azure AD a Azure Active Directory nahrazuje přípony UPN s názvem domény výchozí "onmicrosoft.com".

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Změna příponu UPN z jedné federované domény na jiné federované domény
Azure Active Directory neumožňuje synchronizace UserPrincipalName (UPN) / změnou přípona alternativního přihlašovacího ID z jedné federované domény na jiný federované domény. To platí pro domény, které se ověřují pomocí klienta Azure AD a mají typ ověřování jako federovaný.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD klienta DirSync funkce 'SynchronizeUpnForManagedUsers' je zakázána.
Při vypnuté funkci DirSync klienta Azure AD, SynchronizeUpnForManagedUsers' je Azure Active Directory neumožňuje synchronizace aktualizace UserPrincipalName nebo alternativním přihlašovacím ID pro licencované uživatelské účty s spravované ověřování.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objekt je filtrován kvůli filtrování domény
### <a name="domain-is-not-configured-to-sync"></a>Domény není nakonfigurován pro synchronizaci
Objekt je mimo rozsah z důvodu domény není nakonfigurovaná. V následujícím příkladu objekt je mimo rozsah synchronizace oboru jako domény, který patří do je filtrován z synchronizace.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Doména je nakonfigurována k synchronizaci, ale chybí spuštění profily nebo spustit kroky
Objekt je mimo obor jako domény nebylo nalezeno spustit profily nebo spustit kroky. V následujícím příkladu objekt je mimo rozsah synchronizace oboru jako domény, který patří do chybí spuštění kroky pro úplný Import profilu.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch6.png)

### <a name="object-is-filtered-due-to-ou-filtering"></a>Objekt je filtrovaná kvůli filtrování organizační jednotky
Objekt je mimo rozsah synchronizace obor kvůli konfiguraci filtrování organizační jednotky. V následujícím příkladu objekt náleží do organizační jednotky = NoSync, DC = bvtadwbackdc, DC = com.  Tuto organizační jednotku není součástí oboru synchronizace.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch7.png)

## <a name="html-report"></a>Sestavu ve formátu HTML
Kromě analýzy objekt, vytvoří úlohu řešení potíží také zprávu ve formátu HTML, který obsahuje všechno, co vědět o objektu. Tuto sestavu ve formátu HTML, je možné sdílet s tým podpory udělat další řešení problémů v případě potřeby.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch8.png)

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
