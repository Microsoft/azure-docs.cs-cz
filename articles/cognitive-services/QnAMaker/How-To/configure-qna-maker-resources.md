---
title: Konfigurace služby QnA Maker Service – QnA Maker
description: Tento dokument popisuje pokročilou konfiguraci pro prostředky QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220129"
---
# <a name="configure-qna-maker-resources"></a>Konfigurace prostředků QnA Maker

Uživatel může nakonfigurovat QnA Maker pro použití jiného zdroje vyhledávání v rozpoznávání. Můžou taky nakonfigurovat nastavení služby App Service, pokud používají QnA Maker GA.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurace QnA Maker pro použití jiného prostředku Kognitivní hledání

Pokud vytvoříte službu QnA a její závislosti (například vyhledávání) prostřednictvím portálu, vytvoří se vyhledávací služba pro vás a bude propojena s QnA Makerovou službou. Po vytvoření těchto prostředků můžete aktualizovat nastavení App Service tak, aby používalo dříve existující vyhledávací službu, a odebrat tu, kterou jste právě vytvořili.

Prostředek QnA Maker **App Service** používá prostředek kognitivní hledání. Chcete-li změnit prostředek Kognitivní hledání používaný QnA Maker, je třeba změnit nastavení v Azure Portal.

1. Získejte **klíč správce** a **název** kognitivní hledání prostředku, který chcete QnA Maker použít.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyhledejte **App Service** přidružené k vašemu prostředku QnA maker. Oba mají stejný název.

1. Vyberte **Nastavení** a pak **Konfigurace**. Zobrazí se všechna existující nastavení App Service QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky Azure Portal zobrazující nastavení konfigurace App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Změňte hodnoty následujících klíčů:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Chcete-li použít nové nastavení, je nutné restartovat službu App Service. Vyberte **Přehled** a pak vyberte **restartovat**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky Azure Portal restartování App Service po změně nastavení konfigurace](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Pokud vytváříte službu QnA prostřednictvím šablon Azure Resource Manager, můžete vytvořit všechny prostředky a řídit vytvoření App Service, aby se používala existující vyhledávací služba.

Přečtěte si další informace o tom, jak nakonfigurovat App Service [nastavení aplikace](../../../app-service/configure-common.md#configure-app-settings).

### <a name="get-the-latest-runtime-updates"></a>Získat nejnovější aktualizace modulu runtime

Modul runtime Qnamakerem je součástí instance Azure App Service, která se nasadí při [vytváření služby qnamakerem](./set-up-qnamaker-service-azure.md) v Azure Portal. Aktualizace se pravidelně provádějí do modulu runtime. Instance QnA Maker App Service je v režimu automatické aktualizace po vydání rozšíření lokality z dubna 2019 (verze 5 +). Tato aktualizace je navržená tak, aby se při upgradech postaral o nulové výpadky.

Aktuální verzi můžete zjistit na adrese https://www.qnamaker.ai/UserSettings . Pokud je vaše verze starší než verze 5. x, je nutné restartovat App Service, aby se projevily nejnovější aktualizace:

1. V [Azure Portal](https://portal.azure.com)přejdete do služby qnamakerem (skupina prostředků).

    > [!div class="mx-imgBorder"]
    > ![Qnamakerem skupina prostředků Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Vyberte instanci App Service a otevřete část **Přehled** .

    > [!div class="mx-imgBorder"]
    > ![Instance App Service Qnamakerem](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Restartujte App Service. Proces aktualizace by se měl dokončit během několika sekund. Všechny závislé aplikace nebo roboty, které používají tuto službu Qnamakerem, budou koncovým uživatelům během tohoto intervalu restartování nedostupné.

    ![Restartování instance App Service Qnamakerem](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Nakonfigurujte nastavení nečinné služby App Service tak, aby nevypršel časový limit.

Služba App Service, která slouží jako modul runtime předpovědi QnA Maker pro publikovanou znalostní bázi, má konfiguraci časového limitu nečinnosti, při které se výchozí hodnota automaticky vyprší, pokud je služba nečinná. V případě QnA Maker to znamená, že rozhraní API pro generateAnswer prostředí předplatného občas vyprší časový limit po obdobích bez provozu.

Aby se zajistilo, že se aplikace koncového bodu předpovědi načetla i v případě, že nedochází k provozu, nastavte nečinné na Always On.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte službu App Service prostředku QnA Maker. Bude mít stejný název jako prostředek QnA Maker, ale bude mít jiný **typ** App Service.
1. Najděte **Nastavení** a pak vyberte **Konfigurace**.
1. V podokně Konfigurace vyberte **Obecná nastavení** a pak najít **vždycky zapnuto** **a jako hodnotu** vyberte.

    > [!div class="mx-imgBorder"]
    > ![V podokně Konfigurace vyberte možnost * * Obecné nastavení * *, vyhledejte * * Always On * * a jako hodnotu vyberte * * * * *.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Kliknutím na **Uložit** uložte konfiguraci.
1. Zobrazí se dotaz, jestli chcete aplikaci restartovat, aby používala nové nastavení. Vyberte **Pokračovat**.

Přečtěte si další informace o tom, jak nakonfigurovat App Service [Obecná nastavení](../../../app-service/configure-common.md#configure-general-settings).

### <a name="business-continuity-with-traffic-manager"></a>Kontinuita podnikových aplikací pomocí Traffic Manageru

Hlavním cílem plánu kontinuity podnikových aplikací je vytvořit odolný koncový bod ve znalostní bázi Knowledge Base, který by pro něj neměl nic trvat ani to, jestli ho aplikace spotřebovává.

> [!div class="mx-imgBorder"]
> ![Plán QnA Maker BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Nejdůležitější nápad, jak je znázorněno výše, je následující:

1. Nastavte dvě paralelní [QnA maker služby](set-up-qnamaker-service-azure.md) v [spárovaných oblastech Azure](../../../best-practices-availability-paired-regions.md).

1. [Zálohujte](../../../app-service/manage-backup.md) primární QnA maker App Service a [obnovte](../../../app-service/web-sites-restore.md) ji v sekundární instalaci. Tím se zajistí, že obě nastavení budou fungovat se stejným názvem hostitele a klíči.

1. Udržování primárních a sekundárních indexů Azure Search v synchronizaci. Pomocí ukázky na GitHubu [tady](https://github.com/pchoudhari/QnAMakerBackupRestore) zjistíte, jak zálohovat a obnovit indexy Azure.

1. Zazálohujte Application Insights pomocí [průběžného exportu](../../../azure-monitor/app/export-telemetry.md).

1. Po nastavení primárních a sekundárních zásobníků nakonfigurujte pomocí [Traffic Manageru](../../../traffic-manager/traffic-manager-overview.md) dva koncové body a nastavte metodu směrování.

1. Museli byste vytvořit protokol TLS (Transport Layer Security), dříve označovaný jako SSL (Secure Sockets Layer) (SSL), certifikát pro koncový bod služby Traffic Manager. [Navažte certifikát TLS/SSL](../../../app-service/configure-ssl-bindings.md) ve vašich aplikačních službách.

1. Nakonec v robotu nebo v aplikaci použijte koncový bod Traffic Manageru.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Konfigurace služby QnA Maker spravovaná (Preview) pro použití jiného prostředku Kognitivní hledání

Pokud vytvoříte spravovanou službu QnA (ve verzi Preview) a její závislosti (například vyhledávání) prostřednictvím portálu, vytvoří se vyhledávací služba pro vás a bude propojena se službou QnA Maker Managed (Preview). Po vytvoření těchto prostředků můžete službu Search aktualizovat na kartě **Konfigurace** .

1. V Azure Portal přejdete do služby QnA Maker Managed (Preview).

1. Vyberte **Konfigurace** a vyberte službu Azure kognitivní hledání, kterou chcete propojit se službou QnA maker Managed (Preview).

    ![Snímek obrazovky se stránkou konfigurace spravovaného QnA Maker (Preview)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Klikněte na **Uložit**.

> [!NOTE]
> Pokud změníte Azure Search službu přidruženou k QnA Maker, ztratíte přístup ke všem poznatkům, které už jsou v ní obsažené. Před změnou služby Azure Search se ujistěte, že jste exportovali stávající znalostní báze.

---
