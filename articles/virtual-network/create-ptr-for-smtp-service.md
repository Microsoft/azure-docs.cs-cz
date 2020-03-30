---
title: Konfigurace zón zpětného vyhledávání pro kontrolu banneru SMTP
titlesuffix: Azure Virtual Network
description: Popisuje, jak nakonfigurovat zóny zpětného vyhledávání pro kontrolu banneru SMTP v Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6c37ba61005c9936e6421d06369d1f52b93ac264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201692"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurace zón zpětného vyhledávání pro kontrolu banneru SMTP

Tento článek popisuje, jak použít reverzní zóny v Azure DNS a vytvořit reverzní DNS (PTR) záznam pro Kontrolu bannerů SMTP.

## <a name="symptom"></a>Příznak

Pokud hostujete server SMTP v Microsoft Azure, může se při odesílání nebo přijímání zprávy ze vzdálených poštovních serverů zobrazit následující chybová zpráva:

**554: Žádný záznam PTR**

## <a name="solution"></a>Řešení

Pro virtuální IP adresu v Azure se reverzní záznamy vytvářejí v doménových zónách vlastněných Microsoftem, ne v zónách vlastní domény.

Chcete-li konfigurovat záznamy PTR v zónách vlastněných společností Microsoft, použijte vlastnost -ReverseFqdn v prostředku PublicIpAddress. Další informace najdete [v tématu Konfigurace reverzního DNS pro služby hostované v Azure](../dns/dns-reverse-dns-for-azure-services.md).

Při konfiguraci záznamů PTR se ujistěte, že IP adresa a reverzní vícestranný název domény jsou vlastněny odběrem. Pokud se pokusíte nastavit reverzní reqdn, který nepatří do předplatného, zobrazí se následující chybová zpráva:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn odpovídá fqdn jakéhokoli veřejného prostředku IP v rámci předplatného;
    2) ReverseFqdn překládá na fqdn (prostřednictvím řetězce záznamů CName) libovolného veřejného prostředku IP v rámci předplatného;
    3) Překládá na IP adresu (prostřednictvím CName a řetězce záznamů A) statické veřejné IP prostředku v rámci předplatného.

Pokud ručně změníte banner SMTP tak, aby odpovídal našemu výchozímu zpětnému názvu domény, vzdálený poštovní server může stále selhat, protože může očekávat, že hostitel banneru SMTP bude odpovídat záznamu MX pro doménu.
