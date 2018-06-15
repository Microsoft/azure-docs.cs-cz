---
title: Konfigurace zóny zpětného vyhledávání pro kontrolu banner SMTP v Azure | Microsoft Docs
description: Popisuje postup konfigurace zóny zpětného vyhledávání pro kontrolu banner SMTP v Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/06/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 1e95b00ea08105238a860265e46275c24ed7bfbd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2018
ms.locfileid: "29151870"
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurace zóny zpětného vyhledávání pro kontrolu banner SMTP

Tento článek popisuje, jak používat zóny zpětného vyhledávání v Azure DNS a vytvořte záznam zpětného DNS (PTR) pro SMTP Zkontrolujte informační zpráva. 

## <a name="symptom"></a>Příznaky

Pokud hostujete server SMTP v Microsoft Azure, zobrazí se následující chybová zpráva při odesílat nebo přijímat zprávy pomocí vzdáleného poštovních serverů:

**554: žádný záznam PTR** 

## <a name="solution"></a>Řešení

Pro virtuální IP adresy v Azure jsou vytvořeny zpětné záznamy ve službě Microsoft ve vlastnictví domény zóny, není vlastní domény zóny.

Konfigurace typu PTR v Microsoft ve vlastnictví zón, použijte vlastnost - ReverseFqdn na PublicIpAddress prostředku. Další informace najdete v tématu [konfigurovat reverse pro služby hostované v Azure DNS](../dns/dns-reverse-dns-for-azure-services.md). 

Při konfiguraci záznamů PTR, ujistěte se, že IP adresa a reverzní FQDN jsou vlastněny předplatné. Pokud se pokusíte nastavit reverzní FQDN, která nepatří k předplatnému, zobrazí se následující chybová zpráva:

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) ReverseFqdn odpovídá plně kvalifikovaný název domény jakémukoli prostředku veřejné IP adresy v rámci předplatného; 
    2) ReverseFqdn překládá na plně kvalifikovaný název domény (přes řetěz záznamů CName) jakémukoli prostředku veřejné IP adresy v předplatném; 
    3) Se přeloží na ip adresu (přes řetěz záznamů CName a A) prostředku statické veřejné IP adresy v rámci předplatného. 

Pokud změníte ručně nápisu SMTP tak, aby odpovídaly naše výchozí zpětně plně kvalifikovaný název domény, vzdálené poštovního serveru stále selhat, protože ho může předpokládat, že hostitel SMTP banner tak, aby odpovídaly záznam MX pro doménu.