---
title: Rozšíření Azure MFA serveru NPS nakonfigurovat | Microsoft Docs
description: Po instalaci rozšíření serveru NPS, tyto kroky použijte pro pokročilou konfiguraci, jako je vytvoření seznamu povolených IP a nahrazení UPN.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: d028be90f3a8c06b319faa442f77ab3e5c24dcb7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Upřesnit možnosti konfigurace pro NPS rozšíření pro službu Multi-Factor Authentication

Rozšíření serveru NPS (Network Policy Server) rozšiřuje vaše cloudové funkce Azure Multi-Factor Authentication do místní infrastruktury. Tento článek předpokládá už máte rozšíření nainstalovat a teď chcete vědět, jak přizpůsobit rozšíření pro vás potřebám. 

## <a name="alternate-login-id"></a>Alternativním přihlašovacím ID

Vzhledem k tomu, že rozšíření NPS se připojí k místní a cloudové adresáře, můžete setkat s problémem, kdy se hlavní názvy vaše místní uživatele (UPN) neshodují názvy v cloudu. Chcete-li tento problém vyřešit, použijte alternativních přihlašovacích ID. 

V rámci rozšíření serveru NPS můžete určit atributů služby Active Directory, který má být použit místo názvu UPN pro Azure Multi-Factor Authentication. To umožňuje chránit místní prostředky s dvoustupnovym overovanim beze změny vaší místní UPN. 

Konfigurace alternativního přihlašovacího ID, přejděte na `HKLM\SOFTWARE\Microsoft\AzureMfa` a upravit následující hodnoty registru:

| Název | Typ | Výchozí hodnota | Popis |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | řetězec | Prázdné | Určete název atributu služby Active Directory, který chcete použít místo názvu UPN. Tento atribut slouží jako atribut AlternateLoginId. Pokud tato hodnota registru nastavena na [platný atribut služby Active Directory](https://msdn.microsoft.com/library/ms675090.aspx) (například e-mailu nebo displayName), pak hodnota atributu je použít místo uživatele (UPN) pro ověřování. Pokud tato hodnota registru není prázdný, nebo není nakonfigurováno, pak je zakázána AlternateLoginId a uživatele (UPN) se používá k ověřování. |
| LDAP_FORCE_GLOBAL_CATALOG | Boolean | False | Pomocí tohoto příznaku při vyhledávání AlternateLoginId vynutit použití globálního katalogu pro hledání LDAP. Konfigurace řadiče domény jako globální katalog, přidejte atribut AlternateLoginId do globálního katalogu a potom povolte tento příznak. <br><br> Pokud je nakonfigurovaný (není prázdná), LDAP_LOOKUP_FORESTS **tento příznak se vynucuje jako true**, bez ohledu na to hodnota nastavení registru. V takovém případě rozšíření NPS vyžaduje globální katalog nakonfigurovat s atributem AlternateLoginId pro jednotlivé doménové struktury. |
| LDAP_LOOKUP_FORESTS | řetězec | Prázdné | Zadejte středníky oddělený seznam doménové struktury pro vyhledávání. Například *contoso.com;foobar.com*. Pokud je tato hodnota registru nakonfigurovaný, vyhledá rozšíření NPS interaktivně všech doménových strukturách v pořadí, ve kterém byly uvedeny a vrátí první hodnotu AlternateLoginId úspěšné. Pokud tato hodnota registru není nakonfigurováno, se vyhledávání AlternateLoginId jen do aktuální domény.|

Chcete-li vyřešit potíže s alternativním přihlašovacím ID, použijte doporučené kroky pro [alternativní přihlašovací ID chyby](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Výjimky IP

Pokud je nutné sledovat dostupnost serveru, například pokud ověřte nástroje pro vyrovnávání zatížení serverů, které jsou spuštěny před odesláním úloh, nechcete, aby tyto kontroly zablokuje žádostmi o ověření. Místo toho vytvořte seznam IP adres, které znáte používají účty služeb a zakázání služby Multi-Factor Authentication požadavky pro tento seznam. 

Chcete-li nakonfigurovat seznam povolených IP adres, přejděte na `HKLM\SOFTWARE\Microsoft\AzureMfa` a nakonfigurujte následující hodnotu registru: 

| Název | Typ | Výchozí hodnota | Popis |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | řetězec | Prázdné | Zadejte středníky oddělený seznam IP adres. Zahrnují IP adresy počítačů, kde žádosti o služby mají původ, třeba server NAS nebo virtuální privátní sítě. Rozsahy IP jsou podsítě nejsou podporovány. <br><br> Například *10.0.0.1;10.0.0.2;10.0.0.3*.

Když přijde žádost z IP adresy, která existuje v seznamu povolených serverů, bylo přeskočeno dvoustupňové ověřování. Seznam povolených IP adres se porovná na IP adresu, která je součástí *ratNASIPAddress* atribut požadavku protokolu RADIUS. Pokud požadavku protokolu RADIUS dodává bez atribut ratNASIPAddress, zaznamená se následující upozornění: "P_WHITE_LIST_WARNING::IP povolených je ignorován jako zdrojové IP adresy chybí v požadavku protokolu RADIUS v atributu NasIpAddress."

## <a name="next-steps"></a>Další postup

[řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)