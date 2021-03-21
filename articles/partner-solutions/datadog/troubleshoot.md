---
title: Řešení potíží s služby Datadog – partnerskými řešeními Azure
description: Tento článek poskytuje informace o řešení potíží s služby Datadog v Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563704"
---
# <a name="troubleshooting-datadog-on-azure"></a>Řešení potíží se službou Datadog v Azure

Tento dokument obsahuje informace o řešení potíží s řešeními, která používají služby Datadog.

## <a name="purchase-errors"></a>Chyby nákupu

* Nákup se nezdařil, protože platná platební karta není připojená k předplatnému Azure nebo platební metoda není přidružená k předplatnému.

  Použijte jiné předplatné Azure. Případně můžete přidat nebo aktualizovat platební kartu nebo způsob platby pro předplatné. Další informace najdete v tématu [aktualizace způsobu kreditu a platby](../../cost-management-billing/manage/change-credit-card.md).

* Předplatné EA nepovoluje nákupy na webu Marketplace.

  Použijte jiné předplatné. Případně můžete zjistit, jestli je u předplatného na webu Marketplace povolené předplatné EA. Další informace najdete v tématu [Povolení nákupu na webu Marketplace](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Pokud tyto možnosti problém nevyřeší, obraťte se na [podporu služby Datadog](https://www.datadoghq.com/support).

## <a name="unable-to-create-datadog-resource"></a>Nepovedlo se vytvořit prostředek služby Datadog.

Pokud chcete nastavit integraci Azure služby Datadog, musíte mít oprávnění **vlastníka** k předplatnému Azure. Před zahájením instalace se ujistěte, že máte odpovídající přístup.

## <a name="single-sign-on-errors"></a>Chyby jednotného přihlašování

**Nepovedlo se uložit nastavení jednotného přihlašování** – k této chybě dojde, pokud existuje jiná podniková aplikace, která používá služby DATADOG identifikátor SAML. Pokud chcete zjistit, kterou aplikaci používá, vyberte **Upravit** v části základní konfigurace SAML.

Pokud chcete tento problém vyřešit, buď zakažte jinou aplikaci, nebo použijte jinou aplikaci jako podnikovou aplikaci a nastavte jednotné přihlašování SAML pomocí služby Datadog. Pokud se rozhodnete použít jinou aplikaci, ujistěte se, že má aplikace [požadovaná nastavení](create.md#configure-single-sign-on).

**Aplikace se nezobrazuje na stránce nastavení jednotného přihlašování** – nejdřív vyhledejte ID aplikace. Pokud se nezobrazí žádný výsledek, podívejte se na nastavení SAML aplikace. V mřížce se zobrazují jenom aplikace se správnými nastaveními SAML. 

Adresa URL identifikátoru musí být `https://us3.datadoghq.com/account/saml/metadata.xml` .

Adresa URL odpovědi musí být `https://us3.datadoghq.com/account/saml/assertion` .

Následující obrázek znázorňuje správné hodnoty.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="Ověřte nastavení SAML pro aplikaci služby Datadog v AAD." border="true":::

**Uživatelé typu Host, kteří jsou pozváni do tenanta, nemají přístup k jednotnému přihlašování** – někteří uživatelé mají v Azure Portal dvě e-mailové adresy. Obvykle je jeden e-mail hlavní název uživatele (UPN) a druhý e-mail je alternativní e-mailová adresa.

Při pozvání uživatele typu Host použijte hlavní název uživatele (UPN) pro domácího tenanta. Pomocí hlavního názvu uživatele (UPN) zajistíte synchronizaci e-mailové adresy během procesu jednotného přihlašování. Hlavní název uživatele (UPN) najdete tak, že si vyhledáte e-mailovou adresu v pravém horním rohu Azure Portal uživatele.
  
## <a name="logs-not-being-emitted"></a>Neemitovány protokoly

Protokoly, které se služby Datadog, generují pouze prostředky uvedené v kategorii protokolu prostředků Azure Monitor. Pokud chcete ověřit, jestli prostředek generuje protokoly do služby Datadog, přejděte na nastavení diagnostiky Azure pro konkrétní prostředek. Ověřte, že existuje nastavení diagnostiky služby Datadog.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Nastavení diagnostiky služby Datadog u prostředku Azure" border="true":::

## <a name="metrics-not-being-emitted"></a>Metriky nejsou vydávány

Prostředku služby Datadog se přiřadí role **Čtenář monitorování** v příslušném předplatném Azure. Tato role umožňuje prostředku služby Datadog shromažďovat metriky a odesílat tyto metriky do služby Datadog.

Pokud chcete ověřit, jestli má prostředek správné přiřazení role, otevřete Azure Portal a vyberte předplatné. V levém podokně vyberte možnost **Access Control (IAM)**. Vyhledejte název prostředku služby Datadog. Potvrďte, že prostředek služby Datadog má přiřazení role **Čtenář monitoring** , jak je znázorněno níže.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Přiřazení role služby Datadog v předplatném Azure" border="true":::

## <a name="datadog-agent-installation-fails"></a>Instalace agenta služby Datadog se nezdařila

Integrace služby Azure služby Datadog poskytuje možnost instalace agenta služby Datadog na virtuální počítač nebo App Service. Pro konfiguraci agenta služby Datadog se použije klíč rozhraní API vybraný jako **výchozí klíč** na obrazovce klíče rozhraní API. Pokud není vybraný výchozí klíč, instalace agenta služby Datadog se nezdaří.

Pokud byl agent služby Datadog nakonfigurovaný pomocí nesprávného klíče, přejděte na obrazovku klíče rozhraní API a změňte **výchozí klíč**. Budete muset odinstalovat agenta služby Datadog a přeinstalovat ho, aby se virtuální počítač nakonfiguroval pomocí nových klíčů rozhraní API.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [správě instance](manage.md) služby Datadog.
