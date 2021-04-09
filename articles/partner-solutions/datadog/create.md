---
title: Vytváření služby Datadog – partnerských řešení Azure
description: Tento článek popisuje, jak pomocí Azure Portal vytvořit instanci třídy služby Datadog.
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748394"
---
# <a name="quickstart-get-started-with-datadog"></a>Rychlý Start: Začínáme s služby Datadog

V tomto rychlém startu vytvoříte instanci služby Datadog. Můžete buď vytvořit novou organizaci služby Datadog, nebo propojit s existující organizací služby Datadog.

## <a name="pre-requisites"></a>Požadavky

Pokud chcete nastavit integraci Azure služby Datadog, musíte mít oprávnění **vlastníka** k předplatnému Azure. Před zahájením instalace se ujistěte, že máte odpovídající přístup.

Pokud chcete použít funkci Security Assertion Markup Language (SAML) Single Sign-On (SSO) v prostředku služby Datadog, musíte nastavit podnikovou aplikaci. Chcete-li přidat podnikovou aplikaci, budete potřebovat jednu z těchto rolí: globální správce, správce cloudové aplikace, Správce aplikací nebo vlastník instančního objektu.

K nastavení podnikové aplikace použijte následující postup:

1. Přejít na [Azure Portal](https://portal.azure.com). Vyberte **Azure Active Directory**.
1. V levém podokně vyberte **Podnikové aplikace**.
1. Vyberte **Nová aplikace**.
1. V **části přidat z Galerie** vyhledejte *služby Datadog*. Vyberte výsledek hledání a pak vyberte **Přidat**.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="Služby Datadog aplikaci v galerii AAD Enterprise." border="true":::

1. Po vytvoření aplikace v postranním panelu klikněte na vlastnosti. Nastavte **požadované přiřazení uživatele?** na **ne** a vyberte **Uložit**.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Nastavení vlastností aplikace služby Datadog" border="true":::

1. Na postranním panelu přejdete na **jednotné přihlašování** . Pak vyberte **SAML**.

   :::image type="content" source="media/create/saml-sso.png" alt-text="Ověřování SAML." border="true":::

1. Po zobrazení výzvy k **uložení nastavení jednotného přihlašování** vyberte **Ano** .

   :::image type="content" source="media/create/save-sso.png" alt-text="Uložení jednotného přihlašování pro aplikaci služby Datadog" border="true":::

1. Nastavení jednotného přihlašování je nyní dokončeno.

## <a name="find-offer"></a>Najít nabídku

K vyhledání služby Datadog použijte Azure Portal.

1. Přejděte na web [Azure Portal](https://portal.azure.com/) a přihlaste se.

1. Pokud jste **Marketplace** navštívili v nedávné relaci, vyberte ikonu z dostupných možností. V opačném případě vyhledejte _Marketplace_.

    :::image type="content" source="media/create/marketplace.png" alt-text="Ikona Marketplace":::

1. Na webu Marketplace vyhledejte **služby Datadog**.

1. Na obrazovce Přehled plánu vyberte **nastavit + předplatné**.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Služby Datadog aplikace v Azure Marketplace.":::

## <a name="create-a-datadog-resource-in-azure"></a>Vytvoření prostředku služby Datadog v Azure

Portál zobrazí formulář pro vytvoření prostředku služby Datadog.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Vytvořit prostředek služby Datadog" border="true":::

Zadejte následující hodnoty.

|Vlastnost | Popis
|:-----------|:-------- |
| Předplatné | Vyberte předplatné Azure, které chcete použít k vytvoření prostředku služby Datadog. Musíte mít oprávnění vlastníka. |
| Skupina prostředků | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. [Skupina prostředků](../../azure-resource-manager/management/overview.md#resource-groups) je kontejner, ve kterém jsou příbuzné prostředky řešení Azure. |
| Název prostředku | Zadejte název prostředku služby Datadog. Tento název bude názvem nové organizace služby Datadog při vytváření nové organizace služby Datadog. |
| Umístění | Vyberte USA – západ 2. V současné době je Západní USA 2 jedinou podporovanou oblastí. |
| Služby Datadog organizace | Pokud chcete vytvořit novou organizaci služby Datadog, vyberte **Nová**. Pokud se chcete připojit k existující organizaci služby Datadog, vyberte **existující**. |
| Cenový tarif | Když vytváříte novou organizaci, vyberte ze seznamu dostupných plánů služby Datadog. |
| Fakturační období | Nadpis. |

Pokud odkazujete na stávající organizaci služby Datadog, přečtěte si další část. V opačném případě vyberte **Další: metriky a protokoly** a přeskočte další oddíl.

## <a name="link-to-existing-datadog-organization"></a>Propojení s existující organizací Datadog

Nový prostředek služby Datadog v Azure můžete propojit s existující organizací služby Datadog.

Vyberte **existující** pro datovou organizaci a pak vyberte **propojit s služby Datadog org**.

:::image type="content" source="media/create/link-to-existing.png" alt-text="Odkaz na existující organizaci služby Datadog" border="true":::

Odkaz otevře okno ověřování služby Datadog. Přihlaste se k služby Datadog.

Ve výchozím nastavení Azure propojuje vaši aktuální organizaci služby Datadog s vaším prostředkem služby Datadog. Pokud byste chtěli propojit jinou organizaci, vyberte příslušnou organizaci v okně ověřování, jak je vidět níže.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="Vyberte odpovídající organizaci služby Datadog, kterou chcete propojit." border="true":::

## <a name="configure-metrics-and-logs"></a>Konfigurace metrik a protokolů

Pomocí značek prostředků Azure můžete nakonfigurovat, které metriky a protokoly se odešlou do služby Datadog. Můžete zahrnout nebo vyloučit metriky a protokoly pro konkrétní prostředky.

Pravidla značek pro odesílání **metrik** jsou:

- Ve výchozím nastavení se metriky shromažďují pro všechny prostředky, s výjimkou virtuálních počítačů, služby Virtual Machine Scale Sets a plánů služby App Service.
- Virtuální počítače, služba Virtual Machine Scale Sets a plány služby App Service s *zahrnutím* značek odesílají metriky do služby Datadog.
- Virtuální počítače, sady škálování virtuálních počítačů a plány služby App Service s *vyloučením* značek neodesílají metriky do služby Datadog.
- Pokud dojde ke konfliktu mezi pravidly zahrnutí a vyloučení, má vyloučení přednost.

Pravidla značek pro odesílání **protokolů** jsou:

- Ve výchozím nastavení jsou protokoly shromažďovány pro všechny prostředky.
- Prostředky Azure s použitím značek *include* odesílají protokoly do služby Datadog.
- Prostředky Azure s  *vyloučením* značek neodesílají protokoly do služby Datadog.
- Pokud dojde ke konfliktu mezi pravidly zahrnutí a vyloučení, má vyloučení přednost.

Například níže uvedený snímek obrazovky ukazuje pravidlo značek, ve kterém se metriky odesílají do služby Datadog jenom pro virtuální počítače, virtuální počítače a plány služby App Service, které jsou označené jako *služby Datadog = true* .

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="Nakonfigurujte protokoly a metriky." border="true":::

Existují dva typy protokolů, které lze vygenerovat z Azure do služby Datadog.

1. **Protokoly na úrovni předplatného** – poskytují přehled o operacích na vašich prostředcích na [řídicí rovině](../../azure-resource-manager/management/control-plane-and-data-plane.md). Zahrnují se taky aktualizace událostí služby Service Health. Protokol aktivit použijte k určení toho, kdo a kdy operace zápisu (PUT, POST, DELETE). Pro každé předplatné Azure existuje jeden protokol aktivit.

1. **Protokoly prostředků Azure** – poskytují přehled o operacích provedených na prostředku Azure na [rovině dat](../../azure-resource-manager/management/control-plane-and-data-plane.md). Například získání tajného klíče z Key Vault je operace roviny dat. Nebo vytvoření požadavku na databázi je také operace roviny dat. Obsah protokolů prostředků se liší podle typu prostředku a služby Azure.

Pokud chcete odesílat protokoly na úrovni předplatného na služby Datadog, vyberte **Odeslat protokoly aktivit předplatného**. Pokud je tato možnost ponechána nezaškrtnutá, žádné protokoly na úrovni předplatného se neodesílají do služby Datadog.

Pokud chcete odesílat protokoly prostředků Azure do služby Datadog, vyberte **Odeslat protokoly prostředků Azure pro všechny definované prostředky**. Typy protokolů prostředků Azure jsou uvedené v části [Azure monitor kategorie protokolu prostředků](../../azure-monitor/essentials/resource-logs-categories.md).  K filtrování sady prostředků Azure, které odesílají protokoly do služby Datadog, použijte značky prostředků Azure.  

Protokoly odeslané do služby Datadog se budou účtovat podle Azure. Další informace najdete v tématu [ceny protokolů platforem](https://azure.microsoft.com/pricing/details/monitor/) odesílaných Azure Marketplace partnerům.

Po dokončení konfigurace metrik a protokolů vyberte **Další: jednotné přihlašování**.

## <a name="configure-single-sign-on"></a>Konfigurace jednotného přihlašování

Pokud vaše organizace používá Azure Active Directory jako poskytovatele identity, můžete vytvořit jednotné přihlašování z Azure Portal na služby Datadog. Pokud vaše organizace používá jiného zprostředkovatele identity nebo nechcete v tuto chvíli vytvořit jednotné přihlašování, můžete tuto část přeskočit.

Pokud propojujete prostředek služby Datadog s existující organizací služby Datadog, nemůžete v tomto kroku nastavit jednotné přihlašování. Místo toho jste po vytvoření prostředku služby Datadog nastavili jednotné přihlašování. Další informace najdete v tématu [překonfigurování jednotného přihlašování](manage.md#reconfigure-single-sign-on).

:::image type="content" source="media/create/linking-sso.png" alt-text="Jednotné přihlašování pro propojení s existující organizací služby Datadog" border="true":::

Pokud chcete zavést jednotné přihlašování prostřednictvím Azure Active Directory, zaškrtněte políčko **Povolit jednotné přihlašování prostřednictvím Azure Active Directory**.

Azure Portal načte příslušnou aplikaci v služby Datadog z Azure Active Directory. Aplikace odpovídá podnikové aplikaci, kterou jste zadali v předchozím kroku.

Vyberte název aplikace služby Datadog.

:::image type="content" source="media/create/sso.png" alt-text="Povolte jednotné přihlašování pro služby Datadog." border="true":::

Vyberte **Další: značky**.

## <a name="add-custom-tags"></a>Přidat vlastní značky

Můžete zadat vlastní značky pro nový prostředek služby Datadog. Zadejte páry název-hodnota, které se použijí pro prostředek služby Datadog.

:::image type="content" source="media/create/tags.png" alt-text="Přidejte vlastní značky pro prostředek služby Datadog." border="true":::

Až dokončíte přidávání značek, vyberte **Další: zkontrolovat + vytvořit**.

## <a name="review--create-datadog-resource"></a>Kontrola a vytvoření prostředku služby Datadog

Zkontrolujte svůj výběr a podmínkami použití. Po dokončení ověření vyberte **vytvořit**.

:::image type="content" source="media/create/review-create.png" alt-text="Zkontrolujte a vytvořte prostředek služby Datadog." border="true":::

Azure nasadí prostředek služby Datadog.

Až se proces dokončí, vyberte **Přejít k prostředku** a zobrazí se prostředek služby Datadog.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Nasazení prostředků služby Datadog" border="true":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa prostředku služby Datadog](manage.md)
