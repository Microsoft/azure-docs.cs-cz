---
title: Nastavení souborů cookie proxy aplikace – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Azure Active Directory (Azure AD) má přístup a relační soubory cookie pro přístup k místním aplikacím prostřednictvím proxy aplikace. V tomto článku zjistíte, jak používat a konfigurovat nastavení souborů cookie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481360"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Nastavení souborů cookie pro přístup k místním aplikacím ve službě Azure Active Directory

Azure Active Directory (Azure AD) má přístup a relační soubory cookie pro přístup k místním aplikacím prostřednictvím proxy aplikace. Přečtěte si, jak používat nastavení souborů cookie proxy aplikace. 

## <a name="what-are-the-cookie-settings"></a>Jaké jsou nastavení souborů cookie?

[Proxy aplikace](application-proxy.md) používá následující nastavení souborů cookie pro přístup a relaci.

| Nastavení souborů cookie | Výchozí | Popis | Doporučení |
| -------------- | ------- | ----------- | --------------- |
| Použití souboru cookie pouze pro protokol HTTP | **Ne** | **Ano** umožňuje proxy aplikaci zahrnout příznak HTTPOnly do hlaviček odpovědí HTTP. Tento příznak poskytuje další výhody zabezpečení, například zabraňuje skriptování na straně klienta (CSS) z kopírování nebo úpravy souborů cookie.<br></br><br></br>Předtím, než jsme podporovali nastavení pouze http-only, proxy aplikace šifrované a přenášené cookies přes zabezpečený kanál TLS k ochraně proti změnám. | Použijte **ano** z důvodu dalších výhod zabezpečení.<br></br><br></br>Pro klienty nebo uživatelské agenty, kteří vyžadují přístup k souboru cookie relace, použijte **použít ne.** Například použijte **ne** pro klienta RDP nebo MTSC, který se připojuje k serveru služby Brána vzdálené plochy prostřednictvím proxy aplikace.|
| Použití zabezpečeného souboru cookie | **Ne** | **Ano** umožňuje proxy aplikaci zahrnout příznak Zabezpečení do hlaviček odpovědí HTTP. Zabezpečené soubory cookie zvyšují zabezpečení přenosem souborů cookie prostřednictvím zabezpečeného kanálu TLS, jako je protokol HTTPS. Tím zabráníte tomu, aby byly cookies sledovány neoprávněnými stranami v důsledku přenosu souboru cookie ve prostém textu. | Použijte **ano** z důvodu dalších výhod zabezpečení.|
| Použít trvalý soubor cookie | **Ne** | **Ano** umožňuje proxy aplikaci nastavit, aby jeho přístup cookies nevyprší, když je webový prohlížeč zavřený. Trvalost trvá, dokud vyprší platnost přístupového tokenu nebo dokud uživatel ručně neodstraní trvalé soubory cookie. | Použít **ne** z důvodu bezpečnostního rizika spojeného s udržováním uživatelů ověřených.<br></br><br></br>Doporučujeme používat pouze **ano** pro starší aplikace, které nemohou sdílet soubory cookie mezi procesy. Je lepší aktualizovat aplikaci tak, aby zpracovávala sdílení souborů cookie mezi procesy namísto použití trvalých souborů cookie. Můžete například potřebovat trvalé soubory cookie, které uživateli umožní otevírat dokumenty Office v zobrazení průzkumníka z webu služby SharePoint. Bez trvalých souborů cookie může tato operace selhat, pokud nejsou přístupové soubory cookie sdíleny mezi prohlížečem, procesem průzkumníka a procesem sady Office. |

## <a name="samesite-cookies"></a>Soubory cookie stejného webu
Počínaje verzí Chrome 80 a nakonec v prohlížečích využívajících chrom, budou cookies, které neurčují atribut [SameSite,](https://web.dev/samesite-cookies-explained) považovány za nastavené na **SameSite = Lax**. Atribut SameSite deklaruje, jak by měly být soubory cookie omezeny na kontext stejného webu. Pokud je soubor cookie nastaven na lax, je odeslán pouze na požadavky na stejný web nebo na navigaci nejvyšší úrovně. Proxy aplikace však vyžaduje, aby tyto soubory cookie byly zachovány v kontextu třetích stran, aby uživatelé byli během relace řádně přihlášeni. Z tohoto důvodu provádíme aktualizace souborů cookie pro přístup k proxy aplikaci a relační soubory, abychom zabránili nepříznivému dopadu této změny. Aktualizace zahrnují:

* Nastavení atributu **SameSite** na **žádný**. To umožňuje přístup k proxy aplikacím a soubory cookie relací, které mají být správně odeslány v kontextu třetí strany.
* Nastavení **nastavení Použít zabezpečený soubor cookie** tak, aby se jako výchozí **použilo ano.** Chrome také vyžaduje, aby soubory cookie určily příznak Zabezpečení, jinak bude odmítnut. Tato změna se bude vztahovat na všechny existující aplikace publikované prostřednictvím proxy aplikace. Všimněte si, že soubory cookie přístupu proxy aplikace byly vždy nastaveny na zabezpečené a přenášeny pouze prostřednictvím protokolu HTTPS. Tato změna se bude vztahovat pouze na soubory cookie relace.

Tyto změny souborů cookie aplikace Proxy budou v průběhu příštích několika týdnů před datem vydání prohlížeče Chrome 80.

Navíc pokud vaše back-endová aplikace má soubory cookie, které musí být k dispozici v kontextu třetí strany, musíte explicitně přihlásit změnou aplikace tak, aby používala SameSite=None pro tyto soubory cookie. Proxy aplikace překládá hlavičku Set-Cookie na její adresy URL a bude respektovat nastavení těchto souborů cookie nastavených back-endovou aplikací.



## <a name="set-the-cookie-settings---azure-portal"></a>Nastavení souborů cookie – portál Azure
Nastavení souborů cookie nastavíte pomocí portálu Azure:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Přejděte do **aplikací** > **Azure Active Directory** >Enterprise **Všechny aplikace**.
3. Vyberte aplikaci, pro kterou chcete povolit nastavení souborů cookie.
4. Klepněte na **položku Proxy aplikace**.
5. V části **Další nastavení**nastavte nastavení souboru cookie na **Ano** nebo **Ne**.
6. Kliknutím na **Uložit** použijte změny. 

## <a name="view-current-cookie-settings---powershell"></a>Zobrazit aktuální nastavení souborů cookie - PowerShell

Chcete-li zobrazit aktuální nastavení souborů cookie pro aplikaci, použijte tento příkaz prostředí PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Nastavení souborů cookie – PowerShell

V následujících příkazech ```<ObjectId>``` prostředí PowerShell je Objekt, který je součástí aplikace. 

**Soubor cookie pouze pro protokol Http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Zabezpečený soubor cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Trvalé soubory cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
