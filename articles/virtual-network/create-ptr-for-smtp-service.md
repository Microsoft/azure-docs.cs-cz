---
title: Konfigurace zón zpětného vyhledávání pro kontrolu banneru SMTP
titlesuffix: Azure Virtual Network
description: Popisuje postup konfigurace zón zpětného vyhledávání pro kontrolu banneru SMTP v Azure.
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 0264ad93eb53e27d1dc76f2b20ad175a6ee2f8de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84688683"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurace zón zpětného vyhledávání pro kontrolu banneru SMTP

Tento článek popisuje, jak používat reverzní zónu v Azure DNS a vytvořit reverzní záznam DNS (PTR) pro kontrolu banneru SMTP.

## <a name="symptom"></a>Příznak

Pokud v Microsoft Azure hostuje server SMTP, může se při odesílání nebo přijímání zprávy ze vzdálených poštovních serverů zobrazit následující chybová zpráva:

**554: žádný záznam PTR**

## <a name="solution"></a>Řešení

V případě virtuální IP adresy v Azure se opačné záznamy vytvoří v zónách domény vlastněných společností Microsoft, nikoli v vlastních doménových zónách.

Ke konfiguraci záznamů PTR ve vlastněných zónách Microsoftu použijte vlastnost-ReverseFqdn v prostředku PublicIpAddress. Další informace najdete v tématu [Konfigurace reverzních DNS pro služby hostované v Azure](../dns/dns-reverse-dns-for-azure-services.md).

Když nakonfigurujete záznamy PTR, ujistěte se, že předplatné vlastní IP adresa a reverzní plně kvalifikovaný název domény. Pokud se pokusíte nastavit reverzní plně kvalifikovaný název domény, který nepatří do předplatného, zobrazí se tato chybová zpráva:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn odpovídá plně kvalifikovanému názvu domény každého prostředku veřejné IP adresy v rámci předplatného;
    2) ReverseFqdn se překládá na plně kvalifikovaný název domény (prostřednictvím řetězce záznamů CName) libovolného prostředku veřejné IP adresy v rámci předplatného.
    3) V rámci předplatného se přeloží na IP adresu (prostřednictvím CName a řetězce záznamů) statického prostředku veřejné IP adresy.

Pokud si hlavičku protokolu SMTP ručně změníte tak, aby odpovídala naší výchozí reverzní doméně, může se stát, že vzdálený poštovní server selže, protože by mohl očekávat, že hostitel banneru protokolu SMTP bude odpovídat záznamu MX pro doménu.
