---
title: 'Rychlý Start: nastavení vysoké dostupnosti pomocí služby Azure front-dveří'
description: V tomto rychlém startu se dozvíte, jak používat službu Azure front-dveří pro vysoce dostupnou a vysoce výkonnou globální webovou aplikaci.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2020
ms.author: sharadag
ms.openlocfilehash: e7e500f0459c0f5fd4039acf316d9469e1567a09
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116924"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Rychlý start: Vytvoření Front Dooru pro vysoce dostupnou globální webovou aplikaci

Začněte s předními dvířky Azure pomocí Azure Portal k nastavení vysoké dostupnosti pro webovou aplikaci.

V tomto rychlém startu fond front Azure vytvoří fondy dvou instancí webové aplikace, které běží v různých oblastech Azure. Vytvoříte konfiguraci front-dveří na základě stejné a stejné priority jako u back-endu. Tato konfigurace směruje provoz na nejbližší lokalitu, která spouští aplikaci. Přední dveře Azure neustále monitorují webovou aplikaci. Služba zajišťuje automatické převzetí služeb při selhání pro další dostupnou lokalitu, když nejbližší web není dostupný.

## <a name="prerequisites"></a>Požadované součásti

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Vytvoření dvou instancí webové aplikace

Tento rychlý Start vyžaduje dvě instance webové aplikace, která běží v různých oblastech Azure. Instance webové aplikace běží v režimu *aktivní/aktivní* , takže jedna z nich může přijímat přenosy. Tato konfigurace se liší od *aktivní/úsporné* konfigurace, kdy jedna funguje jako převzetí služeb při selhání.

Pokud ještě nemáte webovou aplikaci, použijte následující postup k nastavení ukázkových webových aplikací.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

1. Z domovské stránky nebo nabídky Azure vyberte **vytvořit prostředek**.

1. Vyberte **Webová** > **Webová aplikace**.

   ![Vytvoření webové aplikace na webu Azure Portal](media/quickstart-create-front-door/create-web-app-for-front-door.png)

1. V části **Webová aplikace**vyberte **předplatné** , které chcete použít.

1. V případě **skupiny prostředků**vyberte **vytvořit novou**. Jako **název** zadejte *FrontDoorQS_rg1* a vyberte **OK**.

1. V části **Podrobnosti o instanci**zadejte jedinečný **název** vaší webové aplikace. V tomto příkladu se používá *WebAppContoso-1*.

1. Vyberte **zásobník modulu runtime**, v tomto příkladu *.NET Core 2,1 (LTS)*.

1. Vyberte oblast, například *střed USA*.

1. V případě **plánu Windows**vyberte **vytvořit novou**. Jako **název** zadejte *MyAppServicePlanCentralUS* a vyberte **OK**.

1. Ujistěte se, že **SKU a velikost** jsou **Standard S1 100 total ACU, 1,75 GB paměti**.

1. Vyberte **zkontrolovat + vytvořit**, zkontrolujte **Souhrn**a pak vyberte **vytvořit**. Dokončení nasazení může trvat několik minut.

   ![Kontrola souhrnu pro webovou aplikaci](media/quickstart-create-front-door/summary-for-web-app-for-front-door.png)

Po dokončení nasazení vytvořte druhou webovou aplikaci. Použijte stejný postup se stejnými hodnotami, s výjimkou následujících hodnot:

| Nastavení          | Hodnota     |
| ---              | ---  |
| **Skupina prostředků**   | Vyberte **Nový** a zadejte *FrontDoorQS_rg2* |
| **Název**             | Zadejte jedinečný název vaší webové aplikace, v tomto příkladu *WebAppContoso-2* .  |
| **Oblast**           | Jiné oblasti, v tomto příkladu *střed USA – jih* |
| **App Service plan** > Plán**Windows** pro plánování App Service         | Vyberte **nové** a zadejte *myAppServicePlanSouthCentralUS*a pak vyberte **OK** . |

## <a name="create-a-front-door-for-your-application"></a>Vytvoření Front Dooru pro vaši aplikaci

Nakonfigurujte přední dveře Azure tak, aby směrovaly uživatelský provoz na základě nejnižší latence mezi dvěma servery webových aplikací. Začněte tím, že přidáte hostitele front-endu pro frontu Azure.

1. Z domovské stránky nebo nabídky Azure vyberte **vytvořit prostředek**. Vyberte **Síťová** > **dvířka**sítě.

1. V **části vytvořit přední dvířka**vyberte **předplatné**.

1. V případě **skupiny prostředků**vyberte **nový**, zadejte *FrontDoorQS_rg0* a vyberte **OK**.  Místo toho můžete použít existující skupinu prostředků.

1. Pokud jste vytvořili skupinu prostředků, vyberte **umístění skupiny prostředků**a potom vyberte **Další: Konfigurace**.

1. Ve **front-endu/doménách**vyberte možnost **+** otevřít **Přidat hostitele front-endu**.

1. Jako **název hostitele**zadejte globálně jedinečný název hostitele. V tomto příkladu se používá *Contoso-Endu*. Vyberte **Přidat**.

   ![Přidání hostitele front-endu pro frontu Azure](media/quickstart-create-front-door/add-frontend-host-for-front-door.png)

Dále vytvořte back-end fond, který obsahuje vaše dvě webové aplikace.

1. Pořád v **části vytvořit frontu** **back-endu**vyberte možnost **+** pro otevření **Přidat back-end fond**.

1. Jako **název**zadejte *myBackendPool*.

1. Vyberte **Přidat back-end**. V případě **typu hostitele back-end**vyberte možnost *App Service*.

1. Vyberte své předplatné a pak zvolte první webovou aplikaci, kterou jste vytvořili z **názvu hostitele back-endu**. V tomto příkladu byla webová aplikace *WebAppContoso-1*. Vyberte **Přidat**.

1. Vyberte znovu **Přidat back-end** . V případě **typu hostitele back-end**vyberte možnost *App Service*.

1. Vyberte své předplatné, znovu a zvolte druhou webovou aplikaci, kterou jste vytvořili z **názvu hostitele back-endu**. Vyberte **Přidat**.

   ![Přidání hostitele back-endu do předních dveří](media/quickstart-create-front-door/add-backend-host-to-pool-for-front-door.png)

Nakonec přidejte pravidlo směrování. Pravidlo směrování namapuje hostitele front-endu do back-endového fondu. Pravidlo přepošle požadavek `contoso-frontend.azurefd.net` na do **myBackendPool**.

1. I když v části **pravidla směrování** **vytvoříte přední dvířka**, vyberte **+** nakonfigurovat pravidlo směrování.

1. Do **Přidat pravidlo**pro **název**zadejte *LocationRule*. Přijměte všechny výchozí hodnoty a pak vyberte **Přidat** a přidejte pravidlo směrování.

   >[!WARNING]
   > **Musíte** zajistit, aby každé z hostitelů front-endu v předních dveřích mělo pravidlo směrování s přidruženou výchozí`\*`cestou (). To znamená, že všechna vaše pravidla směrování musí mít aspoň jedno pravidlo směrování pro každého hostitele ve front-endu definovaného na výchozí cestě (`\*`). Pokud to neuděláte, může to vést k nesprávnému směrování provozu koncového uživatele.

1. Vyberte **zkontrolovat + vytvořit**a pak **vytvořit**.

   ![Nakonfigurované přední dveře Azure](media/quickstart-create-front-door/configuration-of-front-door.png)

## <a name="view-azure-front-door-in-action"></a>Zobrazit přední dveře Azure v akci

Když vytvoříte frontu, může trvat několik minut, než se konfigurace nasadí globálně. Až budete hotovi, přejděte do hostitele front-endu, který jste vytvořili. V prohlížeči, přejít na `contoso-frontend.azurefd.net`. Vaše žádost bude automaticky směrována na nejbližší server z určených serverů ve fondu back-endu.

Pokud jste v tomto rychlém startu vytvořili tyto aplikace, zobrazí se stránka s informacemi.

Pokud chcete otestovat rychlé globální převzetí služeb při selhání v akci, zkuste provést následující kroky:

1. Otevřete prohlížeč, jak je popsáno výše, a přejít na adresu front-endu `contoso-frontend.azurefd.net`:.

1. V Azure Portal vyhledejte a vyberte *App Services*. Posuňte se dolů a najděte jednu z vašich webových aplikací, **WebAppContoso-1** v tomto příkladu.

1. Vyberte webovou aplikaci a vyberte možnost **zastavit**a **Ano** pro ověření.

1. Aktualizujte si stránku v prohlížeči. Měla by se zobrazit stejná informační stránka.

   >[!TIP]
   >Pro tyto akce existuje trochu zpoždění. Možná budete muset aktualizovat znovu.

1. Najděte jinou webovou aplikaci a zastavte ji také.

1. Aktualizujte si stránku v prohlížeči. Tentokrát by se měla zobrazit chybová zpráva.

   ![Obě instance webové aplikace se zastavily.](media/quickstart-create-front-door/service-has-been-stopped.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, můžete odebrat všechny položky, které jste vytvořili. Odstraněním skupiny prostředků se odstraní také její obsah. Pokud nemáte v úmyslu používat tato přední dvířka, měli byste odebrat prostředky, abyste se vyhnuli zbytečným poplatkům.

1. V Azure Portal vyhledejte a vyberte **skupiny prostředků**nebo v nabídce Azure Portal vyberte **skupiny prostředků** .

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

Další informace o směrování provozu najdete v tématu [metody směrování front-dveří](front-door-routing-methods.md).
