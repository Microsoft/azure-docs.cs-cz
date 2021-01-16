---
title: Přehled služby influent cloud – Azure Partnerská řešení Apache Kafka
description: Seznamte se s používáním Apache Kafka v cloudu v cloudu v Azure Marketplace.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: b4aa00b7a2cc7ca6bc968be70957c127a8e00738
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253412"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Co je Apache Kafka pro cloudovou Cloud?

Apache Kafka pro Cloud s nefluentmi je Azure Marketplace nabídka, která poskytuje Apache Kafka jako službu. Je plně spravovaná, takže se můžete soustředit na vytváření aplikací a nemusíte spravovat clustery.

Aby se snížilo zatížení pro různé platformy, společnost Microsoft spolupracuje s cloudovým cloudem a vytvořila integrovanou zřizovací vrstvu z Azure do cloudu. Poskytuje konsolidované prostředí pro využívání cloudu v Azure. Můžete snadno integrovat a spravovat Cloud s využitím svých aplikací Azure.

Dříve museli jste si koupit cloudovou nabídku v Marketplace a samostatně nastavit účet v cloudu v cloudu. Pro správu konfigurací a prostředků jste museli přecházet mezi portály pro cloud Azure a službu influent.

Teď provedete zřizování cloudových prostředků prostřednictvím poskytovatele prostředků s názvem **Microsoft. influent**. Prostředky cloudové organizace v cloudu můžete vytvářet a spravovat prostřednictvím [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/)nebo [sad Azure SDK](/azure/#languages-and-tools). Cloud je v cloudu vlastní a spouští aplikaci SaaS (software jako služba), včetně prostředí, clusterů, témat, klíčů rozhraní API a spravovaných konektorů.

## <a name="capabilities"></a>Možnosti

Hluboká integrace mezi cloudy a Azure nabízí následující možnosti:

- Zřiďte nový prostředek cloudové organizace z Azure Portal s plně spravovanou infrastrukturou.
- Zjednodušte jednotné přihlašování (SSO) z Azure do cloudu s využitím Azure Active Directory (Azure AD). Z cloudového portálu influent není potřeba žádné samostatné ověřování.
- Pomocí fakturace předplatného Azure získáte jednotnou fakturaci využití cloudu.
- Spravujte cloudové prostředky z Azure Portal a sledujte je na stránce **všechny prostředky** pomocí dalších prostředků Azure.

## <a name="confluent-organization"></a>Organizace v Fluent

Nefluent organizace je prostředek, který poskytuje mapování mezi cloudové prostředky Azure a. Je to nadřazený prostředek pro další cloudové prostředky.

Každé předplatné Azure může obsahovat několik plánů s omezením. Každý plán pro plynulé namapovaný na uživatelský účet a organizaci na portálu pro influent. V rámci každé organizace v rámci Fluent můžete vytvořit více prostředí, clusterů, témat a konektorů.

Při zřizování cloudového prostředku v Azure získáte ID organizace, výchozí prostředí a uživatelský účet. Další informace najdete v tématu [rychlý Start: Začínáme s cloudem v Azure](create.md).

Pro účely fakturace se každá cloudová nabídka, která je zakoupená na webu Marketplace, mapuje na jedinečnou organizaci typu influent.

## <a name="single-sign-on"></a>Jednotné přihlašování

Když se přihlásíte k Azure Portal, použijí se k přihlášení na portál Cloud SaaS i vaše přihlašovací údaje. Prostředí využívá [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) a [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) k zajištění bezpečného a pohodlnýho způsobu přihlášení.

Další informace najdete v tématu [jednotné přihlašování](manage.md#single-sign-on).

## <a name="billing"></a>Fakturace

K dispozici jsou dvě možnosti fakturace: plán měsíčních plateb s průběžnými platbami a plán závazku.

- Pomocí **měsíčního plánu** průběžných plateb obdržíte poplatky za využití cloudu v rámci měsíčních faktur Azure.
- S **plánem závazku** si můžete zaregistrovat minimální částku útraty a získat slevu na vaše potvrzené využití cloudu.

Určíte, která možnost fakturace se má použít při vytváření služby.

## <a name="confluent-links"></a>Odkazy v influent

Další nápovědu k používání služby Apache Kafka pro cloudovou ochranu najdete v následujících odkazech na [web služby influent](https://docs.confluent.io/home/overview.html).

Další informace o možnostech fakturace najdete v těchto tématech:

* [Azure Marketplace s průběžnými platbami](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Azure Marketplace se závazky](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

Další informace o správě řešení najdete v těchto tématech:

* [Vytvoření clusteru v cloudu v Fluent](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Cloudová prostředí s antifluenty](https://docs.confluent.io/current/cloud/using/environments.html)
* [Základy cloudu pro influent](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Informace o podpoře a pojmech:

* [Podpora při nefluentu](https://support.confluent.io)
* [Podmínek služby](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit instanci Apache Kafka pro Cloud s ochranou, přečtěte si [rychlý Start: Začínáme s cloudem v Azure](create.md).
