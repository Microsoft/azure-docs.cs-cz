---
title: Řešení potíží s jarním cloudem Azure ve službě Virtual Network
description: Průvodce odstraňováním potíží pro virtuální síť Azure ve jarním cloudu
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: c2b0488663233546411b3a1b5ac1b1eb6c0b899c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98887165"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Řešení potíží s jarním cloudem Azure ve virtuálních sítích

Tento dokument vám pomůže vyřešit různé problémy, které mohou nastat při použití jarního cloudu Azure ve virtuálních sítích.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Došlo k potížím s vytvořením instance služby jarní cloudové služby Azure

Pokud chcete vytvořit instanci Azure jaře cloudu, musíte mít dostatečná oprávnění k nasazení instance do virtuální sítě.  Instance jarní cloudové služby musí sám [udělit oprávnění k virtuální síti cloudové službě Azure pružiny](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Pokud použijete Azure Portal k nastavení instance služby jarní cloudová služba Azure, Azure Portal oprávnění ověří.

Pokud chcete nastavit instanci služby jarní cloudovou službu Azure pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), ověřte, že:

- Předplatné je aktivní.
- Toto umístění podporuje služba Azure jaře Cloud.
- Skupina prostředků pro instanci je už vytvořená.
- Název prostředku odpovídá pravidlu pojmenování. Musí obsahovat jenom malá písmena, číslice a spojovníky. Prvním znakem musí být písmeno. Posledním znakem musí být písmeno nebo číslo. Hodnota musí být v rozmezí 2 až 32 znaků.

Pokud chcete nastavit instanci služby jarní cloudovou službu Azure pomocí šablony Správce prostředků, přečtěte si informace o [struktuře a syntaxi šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

### <a name="common-creation-issues"></a>Běžné problémy při vytváření

| Chybová zpráva | Jak problém vyřešit |
|------|------|
| Prostředky vytvořené pomocí služby Azure jaře Cloud byly zásadami zakázané. | Při nasazení služby jarního cloudu Azure ve vaší vlastní virtuální síti se vytvoří síťové prostředky. Zkontrolujte prosím, jestli máte [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) definované k blokování těchto vytváření. Nepovedlo se vytvořit prostředky, které se dají najít v chybové zprávě. |
| Zadané podsítě mají přidružené k tabulkám směrování, ale zrušte jejich přidružení. | V současné době není podporováno nasazování jarního cloudu Azure v podsíti přidružené k existujícím směrovacím tabulkám. zrušte prosím jejich přidružení a zkuste to znovu. |
| Požadovaný provoz není povolený. | Pokud chcete zajistit, aby byl požadovaný provoz povolený, podívejte se prosím na [zodpovědnost zákazníků při provozování jarního cloudu Azure ve virtuální](spring-cloud-vnet-customer-responsibilities.md) síti. |

## <a name="my-application-cant-be-registered"></a>Moje aplikace se nedá zaregistrovat.

K tomuto problému dochází, pokud je vaše virtuální síť nakonfigurovaná s vlastním nastavením DNS. V takovém případě se privátní zóna DNS používaná v Azure jaře cloudu neprojeví. Přidejte Azure DNS IP 168.63.129.16 jako nadřazený server DNS do vlastního serveru DNS.

## <a name="other-issues"></a>Další problémy

[Řešení běžných problémů s jarním cloudem v Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-troubleshoot)