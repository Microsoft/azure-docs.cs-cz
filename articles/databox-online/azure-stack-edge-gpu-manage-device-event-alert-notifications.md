---
title: Správa oznámení o výstrahách událostí zařízení pro prostředky služby Azure Stack Edge pro Microsoft Docs
description: Popisuje, jak pomocí Azure Portal spravovat výstrahy pro události zařízení v prostředcích služby Azure Stack Edge pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 585343137a4a8fd8a1fb591c640e1183d71c0fd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443093"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Správa oznámení o upozorněních na události zařízení u prostředků Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje, jak vytvořit pravidla akcí v Azure Portal pro aktivaci nebo potlačení oznámení výstrah pro události zařízení, ke kterým dochází v rámci skupiny prostředků, předplatného Azure nebo jednotlivých prostředků Azure Stack Edge. Tento článek se týká všech modelů Azure Stack Edge.  

## <a name="about-action-rules"></a>O pravidlech akcí

Pravidlo akce může aktivovat nebo potlačit oznámení o výstrahách. Pravidlo akce je přidáno do *skupiny akcí* – sada předvoleb oznámení, která se používá k oznámení uživatelům, kteří potřebují pracovat s výstrahami aktivovanými v různých kontextech pro prostředek nebo sadu prostředků.

Další informace o pravidlech akcí najdete v tématu [Konfigurace pravidla akce](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule). Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> Funkce pravidel akcí je ve verzi Preview. Některé obrazovky a kroky se můžou při objemnějším procesu změnit.


## <a name="create-an-action-rule"></a>Vytvořit pravidlo akce

Proveďte následující kroky v Azure Portal k vytvoření pravidla akce pro vaše zařízení Azure Stack Edge.

> [!NOTE]
> Tyto kroky vytvoří pravidlo akce, které odesílá oznámení do skupiny akcí. Podrobnosti o vytvoření pravidla akce pro potlačení oznámení najdete v tématu [Konfigurace pravidla akce](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

1. V Azure Portal přejít na Azure Stack hraniční zařízení a pak na **sledování > výstrahy**. Vyberte **Spravovat akce**.

   ![Monitorování výstrah, Správa zobrazení akcí](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Vyberte **pravidla akcí (Preview)** a pak vyberte **+ nové pravidlo akce**.

   ![Správa akcí, možnost pravidel akcí](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. Na obrazovce **vytvořit pravidlo akce** použijte **obor** k výběru předplatného Azure, skupiny prostředků nebo cílového prostředku. Pravidlo akce bude fungovat pro všechny výstrahy vygenerované v rámci tohoto oboru.

   1. Zvolte **Vybrat** podle **oboru**.

      ![Vyberte obor pro nové pravidlo akce.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Vyberte **předplatné** pro pravidlo akce, volitelně filtrovat podle typu **prostředku** . Pokud chcete vyfiltrovat Azure Stack hraničních prostředků, vyberte **data box Edge zařízení (dataBoxEdge)**.

      Oblast **prostředku** obsahuje seznam dostupných prostředků na základě vašich výběrů.
  
      ![Dostupné prostředky na obrazovce vybrat obor](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Zaškrtněte políčko u každého prostředku, na který chcete pravidlo použít. Můžete vybrat předplatné, skupiny prostředků nebo jednotlivé prostředky. Potom vyberte **Done** (Hotovo).

      ![Ukázková nastavení pro rozsah pravidla akce](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      Na obrazovce **vytvořit pravidlo akce** se zobrazuje vybraný obor.

      ![Dokončený rozsah pro pravidlo akce Azure Stack Edge](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. K zúžení aplikace pravidla na podmnožinu výstrah v rámci vybraného oboru použijte možnosti **filtru** .

   1. Výběrem **Přidat** otevřete podokno **Přidat filtry** .

      ![Možnost pro přidání filtrů do pravidla akce](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. V části **filtry** přidejte každý filtr, který chcete použít. U každého filtru vyberte typ filtru, **operátor** a **hodnotu**.
   
      Seznam možností filtru najdete v tématu [kritéria filtru](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria).

      Níže uvedené ukázkové filtry platí pro všechny výstrahy na úrovních závažnosti 2, 3 a 4, které služba monitorování vyvolává pro Azure Stack hraničních prostředků.

      Až dokončíte přidávání filtrů, vyberte **Hotovo**.
   
      ![Ukázkový filtr pro pravidlo akce](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. Na obrazovce **vytvořit pravidlo akce** vyberte možnost **Skupina akcí** a vytvořte pravidlo, které odesílá oznámení. Pak podle **akcí** zvolte **možnost vybrat**.

   ![Možnost skupiny akcí pro vytvoření pravidla akce, které odesílá oznámení](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Chcete-li vytvořit pravidlo, které potlačí oznámení, vyberte možnost **potlačení**. Další informace najdete v tématu [Konfigurace pravidla akce](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

6. Vyberte skupinu akcí, kterou chcete použít s tímto pravidlem akce. Pak zvolte **Vybrat**. Vaše nové pravidlo akce bude přidáno do předvoleb oznámení vybrané skupiny akcí.

   Pokud potřebujete vytvořit novou skupinu akcí, vyberte **+ vytvořit skupinu akcí** a postupujte podle kroků v [části Vytvoření skupiny akcí pomocí Azure Portal](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal).

   ![Vyberte skupinu akcí pro použití s pravidlem a pak zvolte Vybrat.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Zadejte **název** a **Popis** nového pravidla akce a přiřaďte pravidlo ke skupině prostředků.

9. Nové pravidlo je ve výchozím nastavení povolené. Pokud nechcete pravidlo hned začít používat, vyberte pro možnost **Povolit vytvoření aktualizace pravidla** možnost **ne** .

10. Po dokončení nastavení vyberte **vytvořit**.

    ![Dokončená nastavení pro pravidlo akce, které bude odesílat oznámení o výstrahách](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    Otevře se obrazovka **pravidla akcí (Preview)** , ale nemůžete okamžitě zobrazit vaše nové pravidlo akce. Fokus je **všechny** skupiny prostředků.

11. Chcete-li zobrazit nové pravidlo akce, vyberte skupinu prostředků pro pravidlo.

    ![Obrazovka pravidla akcí s zobrazeným novým pravidlem](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Zobrazení oznámení

Oznámení se dostanou, když nová událost aktivuje výstrahu pro prostředek, který je v oboru pravidla akce.

Skupina akcí pro sady pravidel, která obdrží oznámení a typ oznámení odeslaných e-mailů, zprávy SMS (Short Message Service) nebo obojí.

Až se aktivuje výstraha, může trvat několik minut, než se oznámení dostanou.

E-mailové oznámení budou vypadat podobně jako u tohoto.

![Ukázková e-mailová oznámení pro pravidlo akce](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Další kroky

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Informace o kontrole událostí zařízení, stavu hardwaru a grafech metrik najdete v tématu [monitorování Azure Stack Edge pro](azure-stack-edge-monitor.md) . 
- Informace o optimalizaci Azure Monitor pro zařízení s grafickým procesorem Azure Stack Edge pro najdete v tématu věnovaném [použití Azure monitor](azure-stack-edge-gpu-enable-azure-monitor.md) .
- Informace o správě jednotlivých výstrah najdete v tématu [Vytvoření, zobrazení a Správa upozornění na metriku pomocí cíle odkazů Azure monitor](../azure-monitor/alerts/alerts-metric.md) .