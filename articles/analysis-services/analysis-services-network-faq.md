---
title: Nejčastější dotazy týkající se připojení k síti Analysis Services | Microsoft Docs
description: Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se Analysis Services připojení k síti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "82838497"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Nejčastější dotazy k připojení služby Analysis Services k síti

Tento článek obsahuje odpovědi na běžné dotazy týkající se připojení k účtům úložiště, zdrojům dat, branám firewall a IP adresami.

## <a name="backup-and-restore"></a>Zálohování a obnovení

**Otázka** – jak se dá zálohovat a obnovit pomocí účtu úložiště, který je za bránou firewall?   
**Answer** -Azure Analysis Services nepoužívá pevné IP adresy ani značky služeb. Rozsah IP adres, které vaše Analysis Services servery používají, může být cokoli v rozsahu IP adres pro *oblast Azure*. Vzhledem k tomu, že IP adresy vašeho serveru jsou proměnné a můžou se v průběhu času měnit, musí pravidla brány firewall umožňovat celou škálu IP adres oblasti Azure, ve které je váš server.

**Otázka** – můj účet Azure Storage je v jiné oblasti než Server Analysis Services. Návody nakonfigurovat nastavení brány firewall účtu úložiště?   
**Odpověď** – Pokud je účet úložiště v jiné oblasti, nakonfigurujte nastavení brány firewall účtu úložiště tak, aby povolovala přístup z **vybraných sítí**. V poli **Rozsah adres** brány firewall zadejte rozsah IP adres pro oblast, ve které je Server Analysis Services. Rozsahy IP adres pro oblasti Azure najdete v tématu [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519). Konfigurace nastavení brány firewall účtu úložiště tak, aby povolovala přístup ze všech sítí, ale výběr vybraných sítí a určení rozsahu IP adres jsou preferované. 

**Otázka** – můj účet Azure Storage je ve stejné oblasti jako Server Analysis Services. Jak můžu nakonfigurovat nastavení brány firewall účtu úložiště?   
**Odpověď** – protože váš server Analysis Services a účet úložiště jsou ve stejné oblasti, komunikace mezi nimi používá interní rozsahy IP adres, proto konfigurace brány firewall pro používání vybraných sítí a určení rozsahu IP adres není podporovaná. Pokud zásady organizace vyžadují bránu firewall, je nutné ji nakonfigurovat tak, aby povolovala přístup ze všech sítí.


## <a name="data-source-connections"></a>Připojení zdroje dat

**Otázka** – mám virtuální síť pro svůj systém zdroje dat. Jak můžu Analysis Servicesm serverům dovolit přístup k databázi z virtuální sítě?   
**Odpověď** – Azure Analysis Services se nemůže připojit k virtuální síti. Nejlepším řešením je nainstalovat a nakonfigurovat místní bránu dat ve virtuální síti a potom nakonfigurovat Analysis Services servery pomocí vlastnosti serveru **AlwaysUseGateway** . Další informace najdete v tématu [použití brány pro zdroje dat v Azure Virtual Network (VNET)](analysis-services-vnet-gateway.md).

**Otázka** – mám zdrojovou databázi za bránou firewall. Jak můžu nakonfigurovat bránu firewall, aby k ní měl Server Analysis Services přístup?   
**Answer** -Azure Analysis Services nepoužívá pevné IP adresy ani značky služeb. Rozsah IP adres, které vaše Analysis Services servery používají, může být cokoli v rozsahu IP adres pro *oblast Azure*. V pravidlech brány firewall zdrojové databáze musíte zadat *celý rozsah* IP adres pro oblast Azure vašeho serveru. Další možností je i bezpečnější, alternativou je konfigurace místní brány dat. Pak můžete nakonfigurovat servery Analysis Services s [vlastností serveru AlwaysUseGateway](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)a pak zajistit, aby místní brána dat měla IP adresu povolenou pravidly brány firewall zdroje dat.

## <a name="azure-apps-with-ip-address"></a>Aplikace Azure s IP adresou

**Otázka** – používám aplikaci založenou na Azure (například Azure Functions Azure Data Factory) s IP adresou, která se průběžně mění. Jak můžu spravovat Azure Analysis Services pravidla brány firewall, aby se dynamicky povolovala IP adresa, na které moje aplikace běží?   
**Odpověď** – Azure Analysis Services nepodporuje značky privátních odkazů, virtuální sítěů a služeb. Existují některá open source řešení (například https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) zjišťují IP adresu klientské aplikace a automaticky a dočasně aktualizují pravidla brány firewall).


## <a name="next-steps"></a>Další kroky

[Instalace a konfigurace místní brány dat](analysis-services-gateway-install.md)   
[Připojení k místním zdrojům dat s místní bránou dat](analysis-services-gateway.md)   
[Použití brány pro zdroje dat ve virtuální síti Azure](analysis-services-vnet-gateway.md)
