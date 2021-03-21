---
title: Připojení jako Azure Event Grid partnera pomocí Azure Portal
description: Pomocí Azure Portal připojit Azure Event Gridho partnera.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98050948"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Připojení jako Azure Event Grid partner používající Azure Portal
Tento článek popisuje, jak jsou SaaS poskytovatelé třetích stran, označované také jako vydavatelé událostí nebo partneři, k disEvent Grid, aby mohli publikovat události ze svých služeb a jak jsou tyto události spotřebovány koncovými uživateli.

> [!IMPORTANT]
> Pokud nejste obeznámeni s partnerskými událostmi, přečtěte si téma [Přehled partnerských událostí](partner-events-overview.md) , kde najdete podrobné informace o klíčových konceptech, které jsou důležité pro porozumění, a postupujte podle kroků v tomto článku.

## <a name="onboarding-process-for-event-publishers-partners"></a>Proces připojování pro vydavatele událostí (partneři)
V kostce, který umožňuje, aby se události vaší služby využívané uživateli obvykle zahrnuje následující postup:

1. Než se pustíte do dalších kroků, **sdělte svému** týmu, že se stane partnerem služby Event Grid Service.
1. Vytvořte typ partnerského tématu vytvořením **registrace**. 
1. Vytvořte **obor názvů**.
1. Vytvoření **kanálu událostí** a **partnerského tématu** (jeden krok).
1. Otestujte funkčnost partnerských událostí na konci.

V kroku #4 byste se měli rozhodnout, jaký typ uživatelského prostředí chcete poskytnout. Máte tyto možnosti:
- Poskytněte vlastní řešení, obvykle prostředí grafického uživatelského rozhraní (GUI) hostované v doméně pomocí naší sady SDK a/nebo REST API k vytvoření kanálu událostí a odpovídajícího tématu partnera. Pomocí této možnosti můžete požádat uživatele o předplatné a skupinu prostředků, ve které budete vytvářet téma partnera.
- K vytvoření kanálu událostí a přidruženého partnera použijte Azure Portal nebo CLI. Tato možnost vyžaduje, abyste v předplatném Azure získali nějaký způsob a skupinu prostředků, pod kterou vytvoříte partnera. 

V tomto článku se dozvíte, jak připojit jako partnera Azure Event Grid pomocí Azure Portal. 

> [!NOTE]
> Registrace typu tématu partnera je volitelný krok. Pokud se chcete rozhodnout, jestli byste měli vytvořit partnerský typ tématu, přečtěte si téma [prostředky spravované vydavatelem událostí](partner-events-overview.md#resources-managed-by-event-publishers).

## <a name="communicate-your-interest-in-becoming-a-partner"></a>Informujte svůj zájem o tom, jak se stát partnerem
Vyplňte [Tento formulář](https://aka.ms/gridpartnerform) a obraťte se na Event Grid týmu na adrese [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . Budeme mít konverzaci, která poskytuje podrobné informace o případech použití, osoby, procesu připojování, funkcích, cenách a dalších.

## <a name="prerequisites"></a>Předpoklady
K dokončení zbývajících kroků se ujistěte, že máte:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Tenant](../active-directory/develop/quickstart-create-new-tenant.md)Azure.

## <a name="register-a-partner-topic-type-optional"></a>Registrace typu tématu partnera (volitelné)
1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. V levém navigačním podokně vyberte **všechny služby** a pak na panelu hledání zadejte **Event Grid registrace partnerů** a vyberte je. 
1. Na stránce **Event Grid registrace partnerů** vyberte **+ Přidat** na panelu nástrojů. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="Přidat odkaz na registraci partnera":::
1. Na stránce **vytvoření partnerského tématu typ registrace – základy** zadejte následující informace: 
    1. V části **Project Details (podrobnosti projektu** ) postupujte takto:
        1. Vyberte své **předplatné** Azure. 
        1. Vyberte existující **skupinu prostředků** Azure nebo vytvořte novou skupinu prostředků. 
    1. V části **Podrobnosti registrace** použijte následující postup:
        1. Do pole **název registrace** zadejte název registrace. 
        1. Do pole **název organizace** zadejte název vaší organizace. 
    1. V části **typ partnerského prostředku** zadejte podrobnosti o typu prostředku, který se zobrazí na stránce pro **vytvoření partnerského tématu** : 
        1. Jako **název typu partnerského prostředku** zadejte název typu prostředku. Toto bude typ partnerského tématu, které se vytvoří ve vašem předplatném Azure. 
        2. Do pole **Zobrazovaný název** zadejte uživatelsky přívětivé zobrazované jméno typu partnerského tématu (prostředku). 
        3. Zadejte **Popis typu prostředku**. 
        4. Zadejte **Popis scénáře**. Měli byste vysvětlit způsoby a scénáře, ve kterých se dají používat témata partnerů pro vaše prostředky.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="Vytvořit registraci partnera":::            
1. V dolní části stránky vyberte **Další: vlastní služba** . Na kartě **Služba pro zákazníky** na stránce **vytvořit registraci partnera** zadejte informace, které budou uživatelé předplatitele používat k tomu, aby vás kontaktovali v případě problémů se zdrojem událostí:
    1. Zadejte **telefonní číslo**.
    1. Zadejte **příponu** telefonního čísla.
    1. Zadejte **adresu URL** webu podpory. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="Vytvoření registrace partnera – zákaznické služby":::        
1. Vyberte **Další: značky** v dolní části stránky. 
1. Na stránce **značky** nakonfigurujte následující hodnoty. 
    1. Zadejte **název** a **hodnotu** značky, kterou chcete přidat. Tento krok je **nepovinný**. 
    1. V dolní části stránky vyberte **zkontrolovat + vytvořit** , abyste vytvořili registraci (typ tématu partner).

## <a name="create-a-partner-namespace"></a>Vytvoření oboru názvů partnera

1. V Azure Portal v levém navigačním panelu vyberte **všechny služby** a pak na panelu hledání zadejte **Event Grid obory názvů partnerů** a pak ho vyberte ze seznamu. 
1. Na stránce **Event Grid obory názvů partnerů** vyberte **+ Přidat** na panelu nástrojů. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="Obory názvů partnerů – přidat odkaz":::
1. Na stránce **vytvořit základy oboru názvů partnera** zadejte následující informace.
    1. V části **Project Details (podrobnosti projektu** ) proveďte následující kroky: 
        1. Vyberte **předplatné** Azure.
        1. Vyberte existující **skupinu prostředků** nebo vytvořte skupinu prostředků. 
    1. V části **Podrobnosti oboru názvů** proveďte následující kroky:
        1. Zadejte **název** oboru názvů. 
        1. Vyberte **umístění** pro obor názvů. 
    1. V části **Podrobnosti o registraci** proveďte následující kroky a přidružte obor názvů k registraci partnera. 
        1. Vyberte **předplatné** , ve kterém existuje registrace partnera. 
        1. Vyberte **skupinu prostředků** , která obsahuje registraci partnera. 
        1. V rozevíracím seznamu vyberte **registraci partnera** .
    1. Vyberte **Další: značky** v dolní části stránky.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="Vytvoření stránky s oborem názvů partnera – základy":::
1. Na stránce **značky** přidejte značky (volitelné).
    1. Zadejte **název** a **hodnotu** značky, kterou chcete přidat. Tento krok je **nepovinný**.
    1. V dolní části stránky vyberte **zkontrolovat + vytvořit** .         
1. Na stránce **Revize + vytvořit** si přečtěte podrobnosti a vyberte **vytvořit**. 

## <a name="create-an-event-channel"></a>Vytvoření kanálu událostí
> [!IMPORTANT]
> Musíte požádat uživatele o předplatné Azure, skupinu prostředků, umístění a název partnerského tématu k vytvoření partnerského tématu, které vlastní a spravuje váš uživatel.

1. Přejít na stránku **Přehled** oboru názvů, který jste vytvořili. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="Obor názvů partnera – stránka přehled":::
    partner-namespace-overview.png
1. Na panelu nástrojů vyberte **+ kanál událostí** . 
1. Na stránce **základy vytvoření kanálu událostí** zadejte následující informace. 
    1. V části **Podrobnosti kanálu** proveďte tyto kroky:
        1. Jako **název kanálu událostí** zadejte název kanálu událostí. 
        1. Zadejte **zdroj**. Podívejte se na [specifikace cloudových událostí 1,0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) , které vám pomůžou získat představu o vhodné hodnotě pro zdroj. Podívejte se také na [příklad tohoto schématu cloudových událostí](cloud-event-schema.md#sample-event-using-cloudevents-schema).
    1. V části **Podrobnosti o cíli** zadejte podrobnosti cílového partnerského tématu, které se vytvoří pro tento kanál událostí. 
        1. Zadejte **ID předplatného** , ve kterém se vytvoří partnerské téma. 
        1. Zadejte **název skupiny prostředků** , ve které se vytvoří prostředek partnerského tématu. 
        1. Zadejte **název partnerského tématu**. 
    1. Vyberte **Další: filtry** v dolní části stránky. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Vytvoření stránky základy kanálu událostí":::
1. Na stránce **filtry** přidejte filtry. Proveďte následující kroky:
    1. Filtruje atributy každé události. Dostanou pouze události, které odpovídají všem filtrům. Lze zadat až 25 filtrů. Při porovnávání se nerozlišují malá a velká písmena. Platné klíče používané pro filtry se liší v závislosti na schématu události. V následujícím příkladu,, `eventid` , `source` `eventtype` a `eventtypeversioin` lze použít pro klíče. V datové části dat můžete také použít vlastní vlastnosti pomocí `.` operátoru jako vnořování. Například: `data` , `data.key` , `data.key1.key2` .
    1. Vyberte **Další: Další funkce** v dolní části stránky. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Vytvoření stránky filtrů kanálu událostí":::
        create-event-channel-filters-page.png
1. Na stránce **Další funkce** můžete nastavit **čas vypršení platnosti** a **Popis partnerského tématu**. 
    1. **Čas vypršení platnosti** je čas, kdy se bude téma a přidružený kanál událostí automaticky odstraňovat, pokud ho zákazník neaktivuje. V případě, že není zadaný čas, použije se výchozí hodnota 7 dní. Zaškrtnutím políčka zadáte vlastní čas vypršení platnosti. 
    1. Vzhledem k tomu, že toto téma je prostředkem, který není vytvořen uživatelem, **Popis** může uživatelům pomáhat s porozuměním povaze tohoto tématu. Pokud není nastavená žádná hodnota, bude k dispozici obecný popis. Zaškrtněte políčko pro nastavení popisu vlastního tématu partnera. 
    1. Vyberte **Další: Zkontrolovat a vytvořit**. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Vytvoření kanálu událostí – stránka dalších funkcí":::
1. Na stránce **zkontrolovat + vytvořit** zkontrolujte nastavení a vyberte **vytvořit** a vytvořte tak kanál událostí. 

## <a name="next-steps"></a>Další kroky
- [Témata o partnerech – přehled](./partner-events-overview.md)
- [Formulář pro témata týkající se připojování k partnerům](https://aka.ms/gridpartnerform)
- [Téma Auth0 partner](auth0-overview.md)
- [Jak používat téma Auth0 partner](auth0-how-to.md)