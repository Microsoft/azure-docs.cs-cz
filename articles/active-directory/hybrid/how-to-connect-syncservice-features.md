---
title: Funkce a konfigurace služby Azure AD Connect Sync | Microsoft Docs
description: Popisuje funkce na straně služby pro službu Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261ab5d0f039705a2566b7c28ff4c06778bb661a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410534"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funkce synchronizační služby Azure AD Connect

Funkce synchronizace Azure AD Connect má dvě komponenty:

* Místní součást s názvem **Azure AD Connect synchronizovat**, označovanou také jako **synchronizační modul**.
* Služba, která je umístěná ve službě Azure AD, označovaná také jako **služba Azure AD Connect Sync**

V tomto tématu se dozvíte, jak fungují následující funkce **služby Azure AD Connect Sync** a jak je můžete nakonfigurovat pomocí prostředí Windows PowerShell.

Tato nastavení jsou nakonfigurovaná [modulem Azure Active Directory pro Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)). Stáhněte si ho a nainstalujte samostatně z Azure AD Connect. Rutiny popsané v tomto tématu byly představeny v 2016. březnu vydaných [verzí (build 9031,1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Pokud nemáte rutiny popsané v tomto tématu nebo neposkytují stejný výsledek, ujistěte se, že jste spustili nejnovější verzi.

Pokud chcete zobrazit konfiguraci v adresáři služby Azure AD, spusťte příkaz `Get-MsolDirSyncFeatures` .  
![Výsledek Get-MsolDirSyncFeatures](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Mnohé z těchto nastavení lze změnit pouze pomocí Azure AD Connect.

Následující nastavení lze nakonfigurovat pomocí `Set-MsolDirSyncFeature` :

| DirSyncFeature | Komentář |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Umožňuje objektům připojit se k userPrincipalName kromě primární adresy SMTP. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Umožňuje, aby synchronizační modul aktualizoval atribut userPrincipalName pro spravované a licencované (nefederované) uživatele. |

Po povolení funkce ji nelze znovu zakázat.

> [!NOTE]
> Od 24. srpna 2016 je pro nové adresáře Azure AD ve výchozím nastavení povolená *odolnost duplicitních atributů* funkce. Tato funkce bude také zahrnuta a povolena v adresářích vytvořených před tímto datem. Když se adresář chystá získat tuto funkci, zobrazí se e-mailové oznámení.
> 
> 

Následující nastavení jsou konfigurována nástrojem Azure AD Connect a nelze je upravit pomocí `Set-MsolDirSyncFeature` :

| DirSyncFeature | Komentář |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: povolení zpětného zápisu zařízení](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect synchronizace: přípony adresářů](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency <br/> DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Umožňuje, aby byl atribut umístěn do karantény, pokud je duplicitním jiným objektem a nikoli při exportu. |
| Synchronizace hodnoty hash hesel |[Implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](how-to-connect-password-hash-synchronization.md) |
|Předávací ověřování|[Přihlašování uživatelů s využitím předávacího ověřování služby Azure Active Directory](how-to-connect-pta.md)|
| UnifiedGroupWriteback |Zpětný zápis skupin|
| UserWriteback |Aktuálně se nepodporuje. |

## <a name="duplicate-attribute-resiliency"></a>Odolnost duplicitních atributů

Namísto neúspěšného zřízení objektů s duplicitními názvy UPN/proxyAddresses je duplicitní atribut "v karanténě" a přiřadí se dočasná hodnota. Po vyřešení konfliktu se dočasné jméno UPN změní na správnou hodnotu automaticky. Další podrobnosti najdete v tématu [synchronizace identity a odolnost duplicitních atributů](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Tichá shoda UserPrincipalName

Pokud je tato funkce povolená, je pro hlavní název uživatele (UPN) Kromě [primární adresy SMTP](https://support.microsoft.com/kb/2641663)povolená možnost bez nich, která je vždycky povolená. Pro uživatele cloudu ve službě Azure AD s místními uživateli se používá tichá shoda.

Pokud potřebujete, aby se účty místních účtů služby Active Directory shodovaly se stávajícími účty vytvořenými v cloudu a nepoužíváte Exchange Online, je tato funkce užitečná. V tomto scénáři obecně nemáte důvod k nastavení atributu SMTP v cloudu.

Tato funkce je ve výchozím nastavení zapnutá pro nově vytvořené adresáře Azure AD. Můžete zjistit, jestli je tato funkce povolená, když spustíte:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Pokud tato funkce není pro váš adresář služby Azure AD povolená, můžete ji povolit spuštěním:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchronizovat aktualizace userPrincipalName

Historická aktualizace atributu UserPrincipalName pomocí synchronizační služby z místního prostředí byla zablokována, pokud nebyla splněna obě z těchto podmínek:

* Uživatel je spravován (nefederované).
* Uživateli nebyla přiřazena žádná licence.

> [!NOTE]
> Od března 2019 je povolena synchronizace změn UPN pro účty federovaného uživatele.
> 

Povolení této funkce umožňuje, aby synchronizační modul aktualizoval hodnotu userPrincipalName, když se změní místně a použijete synchronizaci hodnoty hash hesla nebo předávací ověřování.

Tato funkce je ve výchozím nastavení zapnutá pro nově vytvořené adresáře Azure AD. Můžete zjistit, jestli je tato funkce povolená, když spustíte:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Pokud tato funkce není pro váš adresář služby Azure AD povolená, můžete ji povolit spuštěním:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Po povolení této funkce zůstanou existující hodnoty userPrincipalName tak, jak jsou. Při další změně místního atributu userPrincipalName se normální rozdílová synchronizace u uživatelů aktualizuje hlavní název uživatele (UPN).  

## <a name="see-also"></a>Viz také

* [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md)
* [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).