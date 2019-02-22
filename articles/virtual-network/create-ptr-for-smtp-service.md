---
title: Konfigurace zóny zpětného vyhledávání pro kontrolu bannerů protokolu SMTP v Azure
titlesuffix: Azure Virtual Network
description: Popisuje postup konfigurace zóny zpětného vyhledávání pro kontrolu bannerů protokolu SMTP v Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 203c3c5f371af7de891f0949a35378294bb50a0e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652378"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurace zóny zpětného vyhledávání pro kontrolu bannerů protokolu SMTP

Tento článek popisuje způsob použití zóny zpětného vyhledávání v Azure DNS a vytvořte záznam reverzní DNS (PTR) pro zkontrolujte Banner SMTP.

## <a name="symptom"></a>Příznak

Pokud hostujete server SMTP v Microsoft Azure, může se zobrazit následující chybová zpráva při odeslání nebo přijetí zprávy ze serverů vzdálené e-mailu:

**554: Žádný záznam PTR**

## <a name="solution"></a>Řešení

Pro virtuální IP adresy v Azure jsou vytvořeny reverzních záznamů v aplikaci Microsoft vlastní domény zóny, zóny není vlastní doménu.

Ke konfiguraci záznamů PTR v aplikaci Microsoft vlastněných zón, použijte vlastnost - ReverseFqdn prostředků PublicIpAddress. Další informace najdete v tématu [konfigurovat zpětné vyhledávání DNS pro služby hostované v Azure](../dns/dns-reverse-dns-for-azure-services.md).

Při konfiguraci záznamů PTR, ujistěte se, že IP adresa a zpětný plně kvalifikovaný název domény jsou vlastněné předplatné. Pokud se pokusíte nastavit zpětný plně kvalifikovaný název domény, který nepatří k předplatnému, zobrazí se následující chybová zpráva:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn odpovídá plně kvalifikovaný název domény libovolný prostředek veřejné IP adresy v rámci předplatného;
    2) ReverseFqdn překládá plně kvalifikovaný název domény (přes řetěz záznamů CName) libovolný prostředek veřejné IP adresy v rámci předplatného;
    3) Se překládá na ip adresu (přes řetěz záznamů CName a A) prostředku statickou veřejnou IP adresu v rámci předplatného.

Pokud je ručně změnit váš bannerů protokolu SMTP tak, aby odpovídaly naší výchozí reverzní plně kvalifikovaný název domény, vzdálené poštovní server stále selhat, protože ho může předpokládat, že hostitel SMTP banner tak, aby odpovídaly záznam MX pro doménu.
