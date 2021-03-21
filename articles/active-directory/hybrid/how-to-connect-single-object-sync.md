---
title: 'Synchronizace jednoho objektu Azure AD Connect '
description: Přečtěte si, jak synchronizovat jeden objekt ze služby Active Directory do Azure AD kvůli řešení problémů.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726047"
---
# <a name="azure-ad-connect-single-object-sync"></a>Synchronizace jednoho objektu Azure AD Connect 

Nástroj pro synchronizaci s jedním objektem Azure AD Connect je rutina prostředí PowerShell, pomocí které se dá synchronizovat jednotlivý objekt ze služby Active Directory do Azure Active Directory. Vygenerovaná sestava se dá použít k prozkoumání a řešení potíží při synchronizaci objektů. 

> [!NOTE]
> Nástroj podporuje synchronizaci ze služby Active Directory až po Azure Active Directory. Nepodporuje synchronizaci z Azure Active Directory do služby Active Directory. 
>
> Nástroj podporuje synchronizaci úprav objektu přidat a aktualizovat. Nepodporuje synchronizaci úprav objektu DELETE. 

## <a name="how-it-works"></a>Jak to funguje
Nástroj pro synchronizaci s jedním objektem vyžaduje jako vstup rozlišující název služby Active Directory, aby se našel zdrojový konektor a oddíl pro import. Exportuje změny do Azure Active Directory. Nástroj generuje výstup JSON podobný typu prostředku **provisioningObjectSummary** . 

Nástroj pro synchronizaci s jedním objektem provádí následující kroky: 

 1. Určete, jestli má být (zdrojová) doména objektu (konektor služby Active Directory a oddíl) v oboru synchronizace. 
 2. Určete, jestli se má (Azure Active Directory konektoru a oddílu) objektu v oboru synchronizace. 
 3. Určete, zda je organizační jednotka objektu v oboru synchronizace. 
 4. Zjistěte, jestli je objekt přístupný pomocí přihlašovacích údajů k účtu konektoru. 
 5. Určete, zda je typ objektu v oboru synchronizace. 
 6. Určí, jestli je objekt v oboru synchronizace, pokud je povolené filtrování skupin. 
 7. Importujte objekt ze služby Active Directory do prostoru konektoru služby Active Directory. 
 8. Importujte objekt z Azure Active Directory, aby Azure Active Directory místo konektoru. 
 9. Synchronizovat objekt z prostoru konektoru služby Active Directory. 
 10. Exportujte objekt z Azure Active Directory prostoru konektoru do Azure Active Directory. 

Kromě výstupu JSON nástroj vygeneruje sestavu HTML, která obsahuje všechny podrobnosti o operaci synchronizace. Sestava HTML se nachází v **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult. <date> htm**. Tuto sestavu HTML můžete sdílet s týmem podpory a v případě potřeby provádět další odstraňování potíží. 

Sestava HTML má následující: 

|Karta|Description|
|-----|-----|
|Postup|Popisuje kroky podniknuté k synchronizaci objektu. Každý krok obsahuje podrobnosti o řešení potíží. Kroky importu, synchronizace a exportu obsahují další informace o atributu, jako je název, je vícehodnotový typ, hodnota, hodnota přidat, hodnota odstranit, operace, pravidlo synchronizace, typ mapování a zdroj dat.| 
|Řešení potíží & doporučení|Poskytuje kód chyby a důvod. Informace o chybě jsou k dispozici pouze v případě, že dojde k chybě.| 
|Upravené vlastnosti|Zobrazuje starou hodnotu a novou hodnotu. Pokud není k dispozici žádná stará hodnota nebo pokud je nová hodnota odstraněna, je tato buňka prázdná. U vícehodnotových atributů zobrazuje počet. Název atributu je odkaz na kartu kroky: exportovat objekt z Azure Active Directory prostoru konektoru do Azure Active Directory: informace o atributu, jako je třeba název, jsou vícehodnotové, typ, hodnota, hodnota přidat, hodnota odstranit, operace, pravidlo synchronizace, typ mapování a zdroj dat.| 
|Souhrn|Poskytuje přehled o tom, co se stalo, a identifikátory pro objekt ve zdrojovém a cílovém systému.| 

## <a name="prerequisites"></a>Předpoklady 

Chcete-li použít nástroj pro synchronizaci jediného objektu, bude nutné použít 2021. března Azure AD Connect nebo novější verzi. 

### <a name="run-the-single-object-sync-tool"></a>Spusťte nástroj pro synchronizaci s jedním objektem. 

Chcete-li spustit nástroj pro synchronizaci jednoho objektu, proveďte následující kroky: 

 1. Na serveru Azure AD Connect otevřete novou relaci prostředí Windows PowerShell s možností spustit jako správce. 

 2. Nastavte [zásady spouštění](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) na RemoteSigned nebo unstricted. 

 3. Zakažte Plánovač synchronizace po ověření, že neběží žádné operace synchronizace. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. Import modulu diagnostiky AdSync 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Vyvolat rutinu synchronizace s jedním objektem. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Znovu povolte Plánovač synchronizace. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Vstupní parametry synchronizace s jedním objektem|Description| 
|-----|----|
|DistinguishedName|Toto je povinný parametr řetězce. </br></br>Toto je rozlišující název objektu služby Active Directory, který vyžaduje synchronizaci a odstraňování potíží.| 
|StagingMode|Toto je volitelný parametr přepínače.</br></br>Tento parametr lze použít k zabránění exportu změn do Azure Active Directory.</br></br>**Poznámka**: rutina potvrdí operaci synchronizace. </br></br>**Poznámka**: Azure AD Connect přípravnému serveru nebudou exportovány změny do Azure Active Directory.|
|NoHtmlReport|Toto je volitelný parametr přepínače.</br></br>Tento parametr lze použít k zabránění generování sestavy jazyka HTML. 

## <a name="single-object-sync-throttling"></a>Omezení synchronizace s jedním objektem 

Nástroj pro synchronizaci jediného objektu **je** určený k prozkoumání a řešení problémů s synchronizací jednotlivých objektů. **Není určena k** nahrazení synchronizačního cyklu spuštěného plánovačem. Import z Azure Active Directory a export do Azure Active Directory podléhá omezením omezení. Pokud dosáhnete limitu omezení, zkuste to prosím znovu za 5 minut. 

## <a name="next-steps"></a>Další kroky
- [Řešení potíží s synchronizací objektů](tshoot-connect-objectsync.md)
- [Řešení potíží s objektem neprobíhá synchronizace](tshoot-connect-object-not-syncing.md)
- [Komplexní řešení potíží s Azure AD Connect objekty a atributy](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
