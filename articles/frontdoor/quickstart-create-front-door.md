---
title: 'Rychlý Start: nastavení vysoké dostupnosti s využitím služby front-dveří Azure – Azure Portal'
description: V tomto rychlém startu se dozvíte, jak používat službu Azure front-dveří pro vysoce dostupnou a vysoce výkonnou globální webovou aplikaci pomocí Azure Portal.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2020
ms.author: duau
ms.openlocfilehash: 1869098362e37ea18c7ca9a9f827b0e5ec98ea3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067567"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Rychlý start: Vytvoření Front Dooru pro vysoce dostupnou globální webovou aplikaci

Začněte s předními dvířky Azure pomocí Azure Portal k nastavení vysoké dostupnosti pro webovou aplikaci.

V tomto rychlém startu fond front Azure vytvoří fondy dvou instancí webové aplikace, které běží v různých oblastech Azure. Vytvoříte konfiguraci front-dveří na základě stejné a stejné priority jako u back-endu. Tato konfigurace směruje provoz na nejbližší lokalitu, která spouští aplikaci. Přední dveře Azure neustále monitorují webovou aplikaci. Služba zajišťuje automatické převzetí služeb při selhání pro další dostupnou lokalitu, když nejbližší web není dostupný.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Vytvoření dvou instancí webové aplikace

Tento rychlý Start vyžaduje dvě instance webové aplikace, která běží v různých oblastech Azure. Instance webové aplikace běží v režimu *aktivní/aktivní* , takže jedna z nich může přijímat přenosy. Tato konfigurace se liší od *aktivní/úsporné* konfigurace, kdy jedna funguje jako převzetí služeb při selhání.

Pokud ještě nemáte webovou aplikaci, použijte následující postup k nastavení ukázkových webových aplikací.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >   **WebApp**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Vytvoření webové aplikace na webu Azure Portal":::

1. Na kartě **základy** stránky **vytvořit webovou aplikaci** zadejte nebo vyberte následující informace.

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | **Předplatné**               | Vyberte své předplatné. |    
    | **Skupina prostředků**       | Vyberte **vytvořit nový** a do textového pole zadejte *FrontDoorQS_rg1* .|
    | **Název**                   | Zadejte jedinečný **název** vaší webové aplikace. V tomto příkladu se používá *WebAppContoso-1*. |
    | **Publikovat** | Vyberte **Kód**. |
    | **Zásobník modulu runtime**         | Vyberte **.NET Core 2,1 (LTS)**. |
    | **Operační systém**          | Vyberte možnost **Windows**. |
    | **Oblast**           | Vyberte **střed USA**. |
    | **Plán Windows** | Vyberte **vytvořit nový** a do textového pole zadejte *myAppServicePlanCentralUS* . |
    | **SKU a velikost** | Vyberte **úroveň Standard S1 100 celkem ACU, 1,75 GB paměti**. |

1. Vyberte **zkontrolovat + vytvořit**, zkontrolujte **Souhrn** a pak vyberte **vytvořit**. Dokončení nasazení může trvat několik minut.

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="Kontrola souhrnu pro webovou aplikaci":::

Po dokončení nasazení vytvořte druhou webovou aplikaci. Použijte stejný postup se stejnými hodnotami, s výjimkou následujících hodnot:

| Nastavení          | Hodnota     |
| ---              | ---  |
| **Skupina prostředků**   | Vyberte **vytvořit novou** a zadejte *FrontDoorQS_rg2* |
| **Název**             | Zadejte jedinečný název vaší webové aplikace, v tomto příkladu *WebAppContoso-2* .  |
| **Oblast**           | Jiné oblasti, v tomto příkladu *střed USA – jih* |
| **Plán App Service**  >  **Plán Windows**         | Vyberte **nové** a zadejte *myAppServicePlanSouthCentralUS* a pak vyberte **OK** . |

## <a name="create-a-front-door-for-your-application"></a>Vytvoření Front Dooru pro vaši aplikaci

Nakonfigurujte přední dveře Azure tak, aby směrovaly uživatelský provoz na základě nejnižší latence mezi dvěma servery webových aplikací. Začněte tím, že přidáte hostitele front-endu pro frontu Azure.

1. Z domovské stránky nebo nabídky Azure vyberte **vytvořit prostředek**. Vyberte **sítě**  >  **Zobrazit všechny**  >  **přední dveře**.

1. Na kartě **základy** na stránce **vytvořit frontu dveří** zadejte nebo vyberte následující informace a pak vyberte **Další: Konfigurace**.

    | Nastavení | Hodnota |
    | --- | --- |
    | **Předplatné** | Vyberte své předplatné. |    
    | **Skupina prostředků** | Vyberte **vytvořit nový** a do textového pole zadejte *FrontDoorQS_rg0* .|
    | **Umístění skupiny prostředků** | Vyberte **střed USA**. |

1. Ve **front-endu/doménách** vyberte možnost **+** otevřít **Přidat hostitele front-endu**.

1. Jako **název hostitele** zadejte globálně jedinečný název hostitele. V tomto příkladu se používá *Contoso-Endu*. Vyberte **Přidat**.

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Přidání hostitele front-endu pro frontu Azure":::

Dále vytvořte back-end fond, který obsahuje vaše dvě webové aplikace.

1. Pořád v **části vytvořit frontu** **back-endu** vyberte možnost **+** pro otevření **Přidat back-end fond**.

1. Jako **název** zadejte *myBackendPool* a pak vyberte **Přidat back-end**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="Přidat back-end fond":::

1. V okně **Přidat back-end** vyberte následující informace a pak vyberte **Přidat**.

    | Nastavení | Hodnota |
    | --- | --- |
    | **Typ hostitele back-endu** | Vyberte **App Service**. |   
    | **Předplatné** | Vyberte své předplatné. |    
    | **Název hostitele back-endu** | Vyberte první webovou aplikaci, kterou jste vytvořili. V tomto příkladu byla webová aplikace *WebAppContoso-1*. |

    **Ponechte všechna ostatní pole výchozí.*

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="Přidání hostitele back-endu do předních dveří":::

1. Vyberte znovu **Přidat back-end** . Vyberte následující informace a vyberte **Přidat**.

    | Nastavení | Hodnota |
    | --- | --- |
    | **Typ hostitele back-endu** | Vyberte **App Service**. |   
    | **Předplatné** | Vyberte své předplatné. |    
    | **Název hostitele back-endu** | Vyberte druhou webovou aplikaci, kterou jste vytvořili. V tomto příkladu byla webová aplikace *WebAppContoso-2*. |

    **Ponechte všechna ostatní pole výchozí.*

1. V okně **Přidat fond back-endu** vyberte **Přidat** a dokončete konfiguraci fondu back-end.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Přidat back-end fond pro Azure na přední dveře":::

Nakonec přidejte pravidlo směrování. Pravidlo směrování namapuje hostitele front-endu do back-endového fondu. Pravidlo přepošle požadavek `contoso-frontend.azurefd.net` na do **myBackendPool**.

1. I když v části **pravidla směrování** **vytvoříte přední dvířka**, vyberte **+** nakonfigurovat pravidlo směrování.

1. Do **Přidat pravidlo** pro **název** zadejte *LocationRule*. Přijměte všechny výchozí hodnoty a pak vyberte **Přidat** a přidejte pravidlo směrování.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="Přidat pravidlo na přední dveře":::

   >[!WARNING]
   > **Musíte** zajistit, aby každé z hostitelů front-endu v předních dveřích mělo pravidlo směrování s přidruženou výchozí cestou ( `\*` ). To znamená, že všechna vaše pravidla směrování musí mít aspoň jedno pravidlo směrování pro každého hostitele ve front-endu definovaného na výchozí cestě ( `\*` ). Pokud to neuděláte, může to vést k nesprávnému směrování provozu koncového uživatele.

1. Vyberte **zkontrolovat + vytvořit** a pak **vytvořit**.

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="Nakonfigurované přední dveře Azure":::

## <a name="view-azure-front-door-in-action"></a>Zobrazit přední dveře Azure v akci

Když vytvoříte frontu, může trvat několik minut, než se konfigurace nasadí globálně. Až budete hotovi, přejděte do hostitele front-endu, který jste vytvořili. V prohlížeči, přejít na `contoso-frontend.azurefd.net` . Vaše žádost bude automaticky směrována na nejbližší server z určených serverů ve fondu back-endu.

Pokud jste v tomto rychlém startu vytvořili tyto aplikace, zobrazí se stránka s informacemi.

Pokud chcete otestovat rychlé globální převzetí služeb při selhání v akci, zkuste provést následující kroky:

1. Otevřete prohlížeč, jak je popsáno výše, a přejít na adresu front-endu: `contoso-frontend.azurefd.net` .

1. V Azure Portal vyhledejte a vyberte *App Services*. Posuňte se dolů a najděte jednu z vašich webových aplikací, **WebAppContoso-1** v tomto příkladu.

1. Vyberte webovou aplikaci a vyberte možnost **zastavit** a **Ano** pro ověření.

1. Aktualizujte si stránku v prohlížeči. Měla by se zobrazit stejná informační stránka.

   >[!TIP]
   >Pro tyto akce existuje trochu zpoždění. Možná budete muset aktualizovat znovu.

1. Najděte jinou webovou aplikaci a zastavte ji také.

1. Aktualizujte si stránku v prohlížeči. Tentokrát by se měla zobrazit chybová zpráva.

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="Obě instance webové aplikace se zastavily.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, můžete odebrat všechny položky, které jste vytvořili. Odstraněním skupiny prostředků se odstraní také její obsah. Pokud nemáte v úmyslu používat tato přední dvířka, měli byste odebrat prostředky, abyste se vyhnuli zbytečným poplatkům.

1. V Azure Portal vyhledejte a vyberte **skupiny prostředků** nebo v nabídce Azure Portal vyberte **skupiny prostředků** .

1. Vyfiltrujte nebo přejděte dolů a najděte skupinu prostředků, například **FrontDoorQS_rg0**.

1. Vyberte skupinu prostředků a pak vyberte **Odstranit skupinu prostředků**.

   >[!WARNING]
   >Tato akce je irreversable.

1. Zadejte název skupiny prostředků, který chcete ověřit, a pak vyberte **Odstranit**.

Opakujte postup pro ostatní dvě skupiny.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a Naučte se, jak přidat vlastní doménu do front-dveří.
> [!div class="nextstepaction"]
> [Přidání vlastní domény](front-door-custom-domain.md)
