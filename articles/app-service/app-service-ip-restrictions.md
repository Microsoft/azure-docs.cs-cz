---
title: Omezení klienta IP adresy – Azure App Service | Dokumentace Microsoftu
description: Jak použít omezení IP adres pomocí služby Azure App Service
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
ms.date: 7/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 01c948bbfd1883e24dc161d46ba53aef42af0f1d
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250688"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Omezení statickou IP adresu služby Azure App Service #

Omezení IP adres umožňují definovat prioritu seřazený povolit nebo zamítnout seznam IP adres, které můžou přistupovat k vaší aplikace. Seznam povolených tříd může obsahovat adresy IPv4 a IPv6. Pokud je jeden nebo více položek, je pak implicitní Zamítnout vše, která existuje na konci seznamu. 

Funkce omezení podle IP funguje se všemi úlohami, které zahrnují; hostované služby App Service webové aplikace, aplikace api, linuxové aplikace, aplikace kontejneru linuxu a funkce. 

Po odeslání žádosti do vaší aplikace z IP adres je porovnán seznamu omezení IP adres. Pokud adresa nemá povolený přístup na základě pravidel v seznamu, odpoví odpovědí služby s [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) stavový kód.

Funkce omezení podle IP je implementována v role front-endu služby App Service, které jsou upstream hostitelů pracovního procesu, ve kterém běží váš kód. Omezení IP adres se proto prakticky seznamy ACL sítě.  

![Omezení podle IP toku](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Funkce omezení podle IP na portálu dobu, byl vrstvu nad ipSecurity schopností ve službě IIS. Aktuální funkce omezení IP adres se liší. IpSecurity lze přesto nakonfigurovat v souboru web.config aplikace ale front-endu na základě omezení IP adres pravidla budou použita, než všechny přenosy přicházejí služby IIS.

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>Přidávání a úprava pravidel omezení IP adres na portálu ##

Pro přidání pravidla pro omezení IP do vaší aplikace, použijte nabídku otevřete **sítě**>**omezení IP adres** a klikněte na **nakonfigurovat omezení IP adres**

![Možnosti sítě služby App Service](media/app-service-ip-restrictions/ip-restrictions.png)  

V uživatelském rozhraní omezení IP najdete seznam pravidel omezení IP definované pro vaši aplikaci.

![seznam omezení IP adres](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Pokud vaše pravidla byly nakonfigurovány jako tento obrázek, vaše aplikace bude přijímat pouze provoz z 131.107.159.0/24 a by odepřen z libovolné IP adresy.

Můžete kliknout na **[+] přidat** přidáte nové pravidlo omezení IP. Jakmile přidáte pravidlo, se projeví okamžitě. Pravidla se vynucují v pořadí podle priority od nejnižší číslo a směrem nahoru. Je implicitní Zamítnout vše, co je v platnosti, po přidání jednoho pravidla. 

![Přidání pravidla pro omezení IP](media/app-service-ip-restrictions/ip-restrictions-add.png)

IP adresa notation musí zadat v notaci CIDR pro adresy IPv4 a IPv6. Chcete-li určit přesné adresy, můžete použít něco jako 1.2.3.4/32 kde první čtyři oktety představují vaše IP adresa a maska je /32. Je zápis IPv4 CIDR pro všechny adresy 0.0.0.0/0. Další informace o notaci CIDR, si můžete přečíst [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Kliknutím na libovolný řádek, který chcete upravit stávající pravidlo omezení IP. Úpravy platí, okamžitě včetně změn v pořadí priority.

![Upravit pravidlo omezení IP](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Pokud chcete odstranit pravidlo, klikněte na tlačítko **...**  na pravidlo a pak klikněte na tlačítko **odebrat**. 

![Odstranit pravidlo omezení adresy IP](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>Programovou manipulaci se pravidel omezení IP ##

Aktuálně neexistuje žádná prostředí PowerShell nebo rozhraní příkazového řádku pro nové možnosti omezení IP adres, ale hodnoty můžete nastavit ručně pomocí operace PUT v konfiguraci aplikace ve službě Správce prostředků. Například můžete použít resources.azure.com a upravit blok ipSecurityRestrictions přidat požadované JSON. 

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
