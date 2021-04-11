---
title: Pracovní prostory Azure synapse Analytics pro data exfiltrace Protection
description: Tento článek Vysvětlete data exfiltrace Protection ve službě Azure synapse Analytics.
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 5fed7649353c0ef6cb222c0b0c91a5203665e9fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598044"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Pracovní prostory Azure synapse Analytics pro data exfiltrace Protection
Tento článek Vysvětlete data exfiltrace Protection ve službě Azure synapse Analytics.

## <a name="securing-data-egress-from-synapse-workspaces"></a>Zabezpečení odchozích dat z pracovních prostorů synapse
Pracovní prostory Azure synapse Analytics podporují povolení ochrany dat exfiltrace pro pracovní prostory. S exfiltrace Protection můžete chránit před škodlivými insidery, kteří přistupují k prostředkům Azure, a staly všudypřítomnými citlivá data do umístění mimo rozsah vaší organizace. V době vytváření pracovního prostoru se můžete rozhodnout nakonfigurovat pracovní prostor pomocí spravované virtuální sítě a další ochrany před exfiltrace dat. Když se vytvoří pracovní prostor se [spravovanou virtuální sítí](./synapse-workspace-managed-vnet.md), nasadí se prostředky pro integraci dat a Spark do spravované virtuální sítě. Vyhrazené fondy SQL pro pracovní prostor a fondy SQL bez serveru mají možnosti víceklientské architektury a v takovém případě musí existovat mimo spravovanou virtuální síť. U pracovních prostorů s ochranou dat exfiltrace se prostředky v rámci spravované virtuální sítě vždycky sdělují prostřednictvím [spravovaných privátních koncových bodů](./synapse-workspace-managed-private-endpoints.md) a prostředky SQL synapse se můžou připojit jenom k autorizovaným prostředkům Azure (cíli schválených připojení ke spravovaným privátním koncovým bodům z pracovního prostoru). 

> [!Note]
> Po vytvoření pracovního prostoru nemůžete změnit konfiguraci pracovního prostoru pro spravovanou virtuální síť a ochranu dat exfiltrace.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>Správa odchozího přenosu dat v pracovním prostoru synapse do schválených cílů
Po vytvoření pracovního prostoru s povolenou službou data exfiltrace Protection můžou vlastníci prostředku pracovního prostoru spravovat seznam schválených tenantů Azure AD pro daný pracovní prostor. Uživatelé s [oprávněním](./synapse-workspace-access-control-overview.md) k pracovnímu prostoru můžou pomocí nástroje synapse Studio vytvořit spravované požadavky na připojení privátního koncového bodu k prostředkům v autorizovaných klientech Azure AD v pracovním prostoru. Vytvoření spravovaného privátního koncového bodu se zablokuje, pokud se uživatel pokusí vytvořit připojení privátního koncového bodu k prostředku v neschváleném tenantovi.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Ukázkový pracovní prostor s povolenou službou data exfiltrace Protection
Využijte příklad k ilustraci ochrany exfiltrace dat pro pracovní prostory synapse. Společnost Contoso má prostředky Azure v Tenantovi a a klientovi B a je potřeba, aby se tyto prostředky připojovaly bezpečně. V Tenantovi se vytvořil pracovní prostor synapse A s klientem B se přidal jako schválený tenant služby Azure AD. Diagram zobrazuje připojení privátních koncových bodů k Azure Storage účtům v Tenantovi a a Tenantovi B, které schválili vlastníci účtu úložiště. Diagram také ukazuje vytvoření blokovaného privátního koncového bodu. Vytvoření tohoto privátního koncového bodu bylo zablokováno, protože cílí na účet Azure Storage v tenantovi Azure AD společnosti Fabrikam, což není schválený tenant služby Azure AD pro pracovní prostor společnosti Contoso.

:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Tento diagram znázorňuje, jak se implementuje ochrana dat exfiltrace pro pracovní prostory synapse.":::

>[!IMPORTANT]
>
> - Prostředky v klientech jiných než tenant pracovního prostoru musí blokovat pravidla brány firewall, aby se k nim fondy SQL připojovaly. Prostředky v rámci spravované virtuální sítě pracovního prostoru, jako jsou například Clustery Spark, se mohou připojit prostřednictvím spravovaných privátních odkazů na prostředky chráněné bránou firewall.
>
> - Metastore je v pracovních prostorech synapse, které mají spravované Virtual Network se zapnutou ochranou exfiltrace dat, zakázaná. V těchto pracovních prostorech nemůžete použít Spark SQL.
> >

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [vytvořit pracovní prostor s povolenou službou data exfiltrace Protection](./how-to-create-a-workspace-with-data-exfiltration-protection.md) .

Další informace o [spravovaném pracovním prostoru Virtual Network](./synapse-workspace-managed-vnet.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů pro vaše zdroje dat](./how-to-create-managed-private-endpoints.md)
