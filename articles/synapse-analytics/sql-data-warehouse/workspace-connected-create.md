---
title: Povolení funkcí synapse Workspace
description: Tento dokument popisuje, jak může uživatel povolit funkce pracovního prostoru synapse v existujícím vyhrazeném fondu SQL (dřív SQL DW).
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 769ca4cf0ecbdba5fd80d977eb69c8a4f58df55f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98071183"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Povolení funkcí synapse pracovního prostoru pro vyhrazený fond SQL (dřív SQL DW)

Všichni uživatelé SQL Data Warehouse teď můžou získat přístup k existující vyhrazené instanci fondu SQL (dřív SQL DW) a používat ji pomocí synapse studia a pracovního prostoru. Uživatelé můžou synapse Studio a pracovní prostor používat, aniž by to ovlivnilo automatizaci, připojení nebo nástroje. Tento článek vysvětluje, jak existující uživatel Azure synapse Analytics může povolit funkce pracovního prostoru synapse pro existující vyhrazený fond SQL (dřív SQL DW). Uživatel může rozšířit svoje existující analytické řešení díky využití nových funkcí bohatých na funkce, které jsou teď dostupné přes pracovní prostor synapse a Studio.   

## <a name="prerequisites"></a>Požadavky
Než povolíte funkce pracovního prostoru synapse v datovém skladu, musíte se ujistit, že máte následující.
- Práva k vytváření a správě prostředků SQL hostovaných na logickém serveru SQL.
- Práva k vytváření prostředků Azure synapse.
- Azure Active Directory správce identifikovaný na logickém serveru

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Povolení funkcí synapse pracovního prostoru pro existující vyhrazený fond SQL (dřív SQL DW)

Funkce pracovního prostoru synapse se dají povolit u všech existujících vyhrazených fondů SQL (dřív SQL DW) v podporované oblasti. Tato funkce je k dispozici pouze prostřednictvím Azure Portal.

Pomocí těchto kroků můžete vytvořit pracovní prostor synapse pro existující datový sklad.
1. Vyberte existující vyhrazený fond SQL (dřív SQL DW) a otevřete stránku Přehled.
2. V horním panelu nabídek nebo ve zprávě hned dole vyberte **nový pracovní prostor synapse** .
3. Po kontrole seznamu datových skladů, které jsou zpřístupněny prostřednictvím nového pracovního prostoru synapse na stránce **vytvořit novou pracovní prostor Azure synapse** . Pokračujte výběrem **pokračovat** .
4. Na stránce základy je existující vyhrazený fond SQL (oddíl **Project** Details) předem vyplněný stejným **předplatným** a **skupinou prostředků** , která je nasazena v rámci logického serveru. A v části **Podrobnosti o pracovním prostoru** je název **pracovního prostoru** předem vyplněný stejným názvem a oblastí logického serveru SQL, aby bylo zajištěno, že během procesu zřizování bude možné vytvořit připojení mezi novým pracovním prostorem synapse a logickým serverem. Po zřízení tohoto připojení musí být zachováno, aby bylo zajištěno pokračování přístupu k vyhrazeným instancím fondu SQL (dříve SQL DW) prostřednictvím pracovního prostoru a studia.
5. Přejděte k výběru Data Lake Storage Gen 2.
6. Před výběrem možnosti **Další: síť** vyberte **vytvořit novou** nebo vyberte existující **Data Lake Storage Gen2** .
7. Vyberte **heslo správce SQL** pro vaši **instanci bez serveru**. Změna hesla neaktualizuje ani nemění přihlašovací údaje SQL logického serveru. Pokud dáváte přednost heslu definovanému systémem, ponechte tato pole prázdná. Toto heslo můžete kdykoli změnit v novém pracovním prostoru. Jméno správce musí být stejné jako pro SQL Server.
8. Vyberte upřednostňovaná **Síťová nastavení** a vyberte **zkontrolovat + vytvořit** a spusťte zřizování pracovního prostoru.
9. Vyberte **Přejít k prostředku** a otevřete nový pracovní prostor.

    > [!NOTE]
    > Všechny vyhrazené instance fondu SQL (dříve SQL DW) hostované na logickém serveru jsou dostupné přes nový pracovní prostor.

## <a name="post-provisioning-steps"></a>Kroky po zřízení
Abyste se ujistili, že k vašemu existujícím instancím fondu SQL (dříve SQL DW) máte přes synapse Studio k dispozici následující kroky, musíte provést tyto kroky.
1. Na stránce Přehled pracovního prostoru synapse vyberte **připojený server**. **Připojený server** vás přesměruje na připojený logický server SQL, který je hostitelem vašich datových skladů. V nabídce základní vyberte možnost **připojený server**.
2. Otevřete **brány firewall a virtuální sítě** a ujistěte se, že IP adresa klienta nebo předem určený rozsah IP adres mají přístup k logickému serveru.
3. Otevřete **Správce služby Active Directory** a ujistěte se, že na logickém serveru byl nastaven správce AAD.
4. Vyberte jednu z vyhrazených instancí fondu SQL (dříve SQL DW) hostovaných na logickém serveru. Na stránce Přehled vyberte **Spustit synapse Studio** nebo přejít na [přihlášení k synapse studiu](https://web.azuresynapse.net) a přihlaste se do svého pracovního prostoru.

5. Otevřete **centrum dat** a v Průzkumníku objektů rozbalte vyhrazený fond SQL, abyste měli jistotu, že máte přístup a budete moct dotazovat svůj datový sklad.

    > [!NOTE] 
    > Připojený pracovní prostor můžete kdykoli odstranit. Při odstranění pracovního prostoru se neodstraní připojený vyhrazený fond SQL (dřív SQL DW). Funkce pracovního prostoru se dá znovu povolit ve vyhrazeném fondu SQL (dřív SQL DW) po dokončení operace odstranění.

## <a name="next-steps"></a>Další kroky
Začínáme s [pracovním prostorem synapse a nástrojem Studio](../get-started.md).
