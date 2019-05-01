---
title: Omezení přístupu – Azure App Service | Dokumentace Microsoftu
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
ms.date: 04/22/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 558b67b5b0e1ce4f452ce2ca2e97dd7e785c80b6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728695"
---
# <a name="azure-app-service-access-restrictions"></a>Omezení přístupu službě Azure App Service #

Omezení přístupu vám umožňují definovat prioritu seřazený povolit nebo zamítnout seznam, který řídí přístup k síti do vaší aplikace. Tento seznam může obsahovat IP adresy nebo podsítě virtuální sítě Azure. Pokud je jeden nebo více položek, je pak implicitní "Zamítnout vše", která existuje na konci seznamu.

Možnost omezení přístupu funguje s všechny hostované pracovní načte, jako je například služby App Service webové aplikace, aplikace API, Linuxové aplikace, aplikace kontejneru Linuxu a funkce.

Po odeslání žádosti do své aplikace adresa odesílatele je porovnán pravidla IP adres v seznamu omezení přístupu. Pokud adresa odesílatele je v podsíti, který je nakonfigurovaný s koncovými body služby chcete Microsoft.Web, zdrojová podsíť bude porovnána s pravidel virtuální sítě v seznamu omezení přístupu. Pokud adresa nemá povolený přístup na základě pravidel v seznamu, odpoví odpovědí služby s [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) stavový kód.

Možnost omezení přístupu je implementované v role front-endu služby App Service, které jsou upstream hostitelů pracovního procesu, ve kterém běží váš kód. Omezení přístupu se proto prakticky seznamy ACL sítě.

Možnost omezení přístupu k vaší webové aplikace ze služby Azure Virtual Network (VNet) je volána [koncové body služby][serviceendpoints]. Koncové body služby umožňují omezit přístup k víceklientské služby z vybrané podsítě. Musí být povolená na straně sítě i služba, která je právě povoleno pomocí. 

![Postup pro omezení přístupu](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Přidání a úpravu pravidel omezení přístupu na portálu ##

Přidat pravidlo pro omezení přístupu k vaší aplikaci, použijte nabídku otevřete **sítě**>**omezení přístupu** a klikněte na **konfigurovat omezení přístupu**

![Možnosti sítě služby App Service](media/app-service-ip-restrictions/access-restrictions.png)  

V uživatelském rozhraní omezení přístupu najdete seznam pravidel pro omezení přístupu definované pro vaši aplikaci.

![omezení přístupu seznamu](media/app-service-ip-restrictions/access-restrictions-browse.png)

Seznamu se zobrazí všechny aktuální omezení, které jsou ve vaší aplikaci. Pokud máte omezení virtuální sítě ve vaší aplikaci, v tabulce se zobrazí, pokud jsou povolené koncové body služby pro Microsoft.Web. Pokud nejsou žádná omezení definovaná ve vaší aplikaci, bude vaše aplikace přístupná odkudkoli.  

Můžete kliknout na **[+] přidat** přidáte nové pravidlo omezení přístupu. Jakmile přidáte pravidlo, se projeví okamžitě. Pravidla se vynucují v pořadí podle priority od nejnižší číslo a směrem nahoru. Je implicitní Zamítnout vše, co je v platnosti, po přidání jednoho pravidla.

![Přidání pravidla pro omezení přístupu IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Při vytváření pravidla, musíte vybrat povolit nebo zamítnout a také typ pravidla. Také musíte zajistit, že hodnota priority a co se omezení přístupu.  Volitelně můžete přidat název a popis pravidla.  

Chcete-li nastavit IP adresu na základě pravidel, vyberte typ protokolu IPv4 nebo IPv6. IP adresa notation musí zadat v notaci CIDR pro adresy IPv4 a IPv6. Chcete-li určit přesné adresy, můžete použít něco jako 1.2.3.4/32 kde první čtyři oktety představují vaše IP adresa a maska je /32. Je zápis IPv4 CIDR pro všechny adresy 0.0.0.0/0. Další informace o notaci CIDR, si můžete přečíst [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

![Přidat omezovací pravidlo přístupu virtuální sítě](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

K omezení přístupu k vybrané podsítě, vyberte typ virtuální sítě. Nižší, než budete moct vybrat předplatné, virtuální síť a podsíť, ve které chcete povolit nebo odepřít přístup s. Pokud koncové body služby nejsou ještě povolené s Microsoft.Web pro podsíť, kterou jste vybrali, bude automaticky povoleno pro vás Pokud zaškrtněte políčko s dotazem, není to udělat. Situace, kde byste měli povolit na aplikaci, ale ne podsítě je do značné míry související s Pokud máte oprávnění k povolení koncových bodů služby v podsíti, nebo ne. Pokud potřebujete získat někdo jiný se povolit koncové body služby v podsíti, můžete zaškrtnout políčko a jste aplikaci nakonfigurovali pro koncové body služby čekat na případná neodebraly později v podsíti. 

Kliknutím na libovolný řádek, který chcete upravit stávající pravidlo omezení přístupu. Úpravy platí, okamžitě včetně změn v pořadí priority.

![Upravit pravidlo pro omezení přístupu](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Při úpravě pravidla nelze změnit typ mezi pravidlo IP adres a pravidlo virtuální sítě. 

![Upravit pravidlo pro omezení přístupu](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Pokud chcete odstranit pravidlo, klikněte na tlačítko **...**  na pravidlo a pak klikněte na tlačítko **odebrat**.

![Odstranit pravidlo přístupu k omezení](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="scm-site"></a>Web SCM 

Kromě toho, že řídit přístup k vaší aplikaci, můžete taky omezit přístup k webu scm se používají ve vaší aplikaci. Web scm se na webu nasadit koncový bod a také konzole Kudu. Samostatně můžete přiřadit omezení přístupu k webu scm z aplikace nebo používat stejné nastavení pro aplikaci a Web scm. Při vrácení se změnami do pole má stejná omezení jako aplikace, všechno, co je ignorováno navýšení kapacity. Pokud jste zrušte zaškrtnutí políčka, jakékoli nastavení bylo dříve na webu Správce řízení služeb se použijí. 

![omezení přístupu seznamu](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

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

Omezení IP adres jsou k dispozici pro obě aplikace Function App pomocí stejné funkce jako plány služby App Service. Povolení omezení IP adres zakážete editoru portálu kódu pro každé zakázaných IP adresy.

[Další informace najdete tady](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
