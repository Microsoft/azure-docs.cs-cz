---
title: Omezení klienta IP adresy – Azure App Service | Dokumentace Microsoftu
description: Použití omezení přístupu pomocí služby Azure App Service
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bb6ab29f02282a394e3f93e41682ceaec5208b75
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357612"
---
# <a name="azure-app-service-static-access-restrictions"></a>Omezení statických přístupu službě Azure App Service #

Omezení přístupu vám umožňují definovat prioritu seřazený povolit nebo zamítnout seznam IP adres, které můžou přistupovat k vaší aplikace. Seznam povolených tříd může obsahovat adresy IPv4 a IPv6. Pokud je jeden nebo více položek, je pak implicitní Zamítnout vše, která existuje na konci seznamu.

Možnost omezení přístupu funguje se všemi úlohami, které zahrnují; hostované služby App Service webové aplikace, aplikace API, Linuxové aplikace, aplikace kontejneru Linuxu a funkce.

Po odeslání žádosti do vaší aplikace z IP adres je porovnán seznamu omezení přístupu. Pokud adresa nemá povolený přístup na základě pravidel v seznamu, odpoví odpovědí služby s [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) stavový kód.

Omezení přístupu funkce je implementována v role front-endu služby App Service, které jsou upstream hostitelů pracovního procesu, ve kterém běží váš kód. Omezení přístupu se proto prakticky seznamy ACL sítě.  

![Postup pro omezení přístupu](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Možnost omezení přístupu na portálu dobu, byl vrstvu nad ipSecurity schopností ve službě IIS. Aktuální omezení přístupu funkce se liší. IpSecurity lze přesto nakonfigurovat v souboru web.config aplikace ale front-endu na základě pravidel omezení přístupu se použijí před všechny přenosy přicházejí služby IIS.

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Přidání a úpravu pravidel omezení přístupu na portálu ##

Přidat pravidlo pro omezení přístupu k vaší aplikaci, použijte nabídku otevřete **sítě**>**omezení přístupu** a klikněte na **konfigurovat omezení přístupu**

![Možnosti sítě služby App Service](media/app-service-ip-restrictions/ip-restrictions.png)  

V uživatelském rozhraní omezení přístupu najdete seznam pravidel pro omezení přístupu definované pro vaši aplikaci.

![omezení přístupu seznamu](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Pokud vaše pravidla byly nakonfigurovány jako tento obrázek, vaše aplikace bude přijímat pouze provoz z 131.107.159.0/24 a by odepřen z libovolné IP adresy.

Můžete kliknout na **[+] přidat** přidáte nové pravidlo omezení přístupu. Jakmile přidáte pravidlo, se projeví okamžitě. Pravidla se vynucují v pořadí podle priority od nejnižší číslo a směrem nahoru. Je implicitní Zamítnout vše, co je v platnosti, po přidání jednoho pravidla.

![Přidání pravidla pro omezení přístupu](media/app-service-ip-restrictions/ip-restrictions-add.png)

IP adresa notation musí zadat v notaci CIDR pro adresy IPv4 a IPv6. Chcete-li určit přesné adresy, můžete použít něco jako 1.2.3.4/32 kde první čtyři oktety představují vaše IP adresa a maska je /32. Je zápis IPv4 CIDR pro všechny adresy 0.0.0.0/0. Další informace o notaci CIDR, si můžete přečíst [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Kliknutím na libovolný řádek, který chcete upravit stávající pravidlo omezení přístupu. Úpravy platí, okamžitě včetně změn v pořadí priority.

![Upravit pravidlo pro omezení přístupu](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Pokud chcete odstranit pravidlo, klikněte na tlačítko **...**  na pravidlo a pak klikněte na tlačítko **odebrat**.

![Odstranit pravidlo přístupu k omezení](media/app-service-ip-restrictions/ip-restrictions-delete.png)

Můžete taky omezit přístup k nasazení na další kartě. K přidání/úprava/odstranění každé pravidlo, postupujte podle stejného kroku, jak je uvedeno výše.

![omezení přístupu seznamu](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programovou manipulaci se pravidla pro omezení přístupu ##

Aktuálně neexistuje žádná prostředí PowerShell nebo rozhraní příkazového řádku pro nové možnosti omezení přístupu, ale hodnoty můžete nastavit ručně pomocí operace PUT v konfiguraci aplikace ve službě Správce prostředků. Například můžete použít resources.azure.com a upravit blok ipSecurityRestrictions přidat požadované JSON.

Umístění těchto informací v Resource Manageru je:

Management.Azure.com/subscriptions/**ID předplatného**/resourceGroups/**skupiny prostředků**/providers/Microsoft.Web/sites/**název webové aplikace**  /config/web? verze API-version = 2018-02-01

Syntaxe JSON předchozího příkladu je:

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],

## <a name="function-app-ip-restrictions"></a>Omezení IP adres aplikace – funkce

Omezení IP adres jsou k dispozici pro obě aplikace Function App pomocí stejné funkce jako plány služby App Service. Všimněte si, že povolíte IP zakáže omezení editoru portálu kódu pro každé zakázaných IP adresy.

[Další informace najdete tady](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)