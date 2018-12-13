---
title: Příprava pro odchozí IP adresa změnit - službě Azure App Service
description: Pokud váš odchozí IP adresa je změnit, zjistěte, co můžete udělat tak, aby vaše aplikace i nadále fungovat po provedení změny.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: f611733400b8bfd18a135fe07f84606f13e59704
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275706"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Postup přípravy na změnu odchozí IP adresy

Pokud jste dostali oznámení, že se mění odchozí IP adresy vaší aplikace Azure App Service, postupujte podle pokynů v tomto článku.

## <a name="determine-if-you-have-to-do-anything"></a>Zjistit, pokud máte dělat nic.

* Možnost 1: Pokud vaše aplikace app Service nepoužívá filtrování protokolu IP, explicitní zařazení seznamu nebo zvláštní zacházení odchozí provoz, jako je směrování nebo brány firewall, nemusíte nic dělat.

* Možnost 2: Pokud aplikace nemá zvláštní zacházení odchozí IP adresy (Další příklady naleznete níže), přidat nové odchozí IP adresy, bez ohledu na to se zobrazí ta stávající. Nemáte nahradit stávající IP adresy. Podle pokynů uvedených v následující části najdete nové odchozí IP adresy.

  Například odchozí IP adresa může být výslovně zahrnuty v bráně firewall mimo vaši aplikaci nebo službě externí platby může mít seznamu povolených, který obsahuje odchozí IP adresa pro vaši aplikaci. Pokud odchozí adresy je nakonfigurované v seznamu kdekoli mimo aplikaci, který musí změnit.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Zjistit odchozí IP adresy na webu Azure Portal

Nové odchozí IP adresy se zobrazí na portálu předtím, než se projeví. Při spuštění pomocí nové značky Azure staré už nebude používat. Najednou pouze jednu sadu se používá, takže položky v seznamech povolených položek, musí mít staré a nové IP adresy zabránit výpadku v případě přepínače. 

1.  Otevřete web [Azure Portal](https://portal.azure.com).

2.  V levé navigační nabídce vyberte **App Services**.

3.  Vyberte ze seznamu vaši aplikaci služby App Service.

1.  Pokud je aplikace function app, naleznete v tématu [funkce aplikace odchozí IP adresy](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  V části **nastavení** záhlaví, klikněte na tlačítko **vlastnosti** v levém navigačním panelu a najít v části s názvem **odchozích IP adres**.

5. Zkopírování IP adresy a přidat je do vašeho zvláštní zacházení odchozího provozu, jako je například filtr nebo seznamu povolených aplikací. Neodstraňovat existujících IP adres v seznamu.

## <a name="next-steps"></a>Další postup

Tento článek vysvětlil, jak připravit pro změnu IP adresy, který byl inicializován nástrojem Azure. Další informace o IP adresách v Azure App Service najdete v tématu [příchozí a odchozí IP adresy ve službě Azure App Service](app-service-ip-addresses.md).
