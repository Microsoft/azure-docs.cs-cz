---
title: Běžné úlohy správy cloudových služeb | Dokumenty společnosti Microsoft
description: Přečtěte si, jak spravovat cloudové služby na webu Azure Portal. Tyto příklady používají portál Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 80481bc11933b0404079221f23b5054024f00acb
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811364"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Správa cloudových služeb na webu Azure Portal
V oblasti **Cloudservices** na portálu Azure můžete:

* Aktualizujte roli služby nebo nasazení.
* Propagujte fázované nasazení do produkčního prostředí.
* Propojte prostředky s cloudovou službou, abyste viděli závislosti prostředků a škálovat prostředky společně.
* Odstraňte cloudovou službu nebo nasazení.

Další informace o škálování cloudové služby najdete [v tématu Konfigurace automatickéškálování pro cloudovou službu na portálu](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Aktualizace role nebo nasazení cloudové služby
Pokud potřebujete aktualizovat kód aplikace pro cloudovou službu, použijte **update** na okně cloudové služby. Můžete aktualizovat jednu roli nebo všechny role. Chcete-li aktualizovat, můžete nahrát nový balíček služby nebo konfigurační soubor služby.

1. Na [webu Azure Portal][Azure portal]vyberte cloudovou službu, kterou chcete aktualizovat. Tento krok otevře okno instance cloudové služby.

2. Na noži vyberte **Aktualizovat**.

    ![Tlačítko Aktualizovat](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Aktualizujte nasazení novým souborem balíčku služby (.cspkg) a konfiguračním souborem služby (.cscfg).

    ![Aktualizovatnasazení](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Volitelně můžete aktualizovat účet úložiště a popisek nasazení.

5. Pokud některé role mají pouze jednu instanci role, zaškrtněte **políčko Nasadit i v případě, že jedna nebo více rolí obsahuje jednu instanci,** aby upgrade mohl pokračovat.

    Azure může zaručit pouze 99,95 procent dostupnosti služby během aktualizace cloudové služby, pokud každá role má alespoň dvě instance role (virtuální počítače). Se dvěma instancemi rolí jeden virtuální počítač zpracovává požadavky klientů, zatímco druhý je aktualizován.

6. Zaškrtněte políčko **Zahájit nasazení,** chcete-li aktualizaci nainstalovat po dokončení nahrávání balíčku.

7. Chcete-li službu začít aktualizovat, vyberte **možnost OK.**

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Výměna nasazení za účelem podpory fázovaného nasazení v produkčním prostředí
Když se rozhodnete nasadit novou verzi cloudové služby, fáze a otestovat novou verzi v testovacím prostředí cloudové služby. Pomocí **funkce Swap** můžete přepnout adresy URL, kterými jsou obě nasazení adresována, a propagovat novou verzi v produkčním prostředí.

Nasazení můžete vyměnit ze stránky **Cloudových služeb** nebo z řídicího panelu.

1. Na [webu Azure Portal][Azure portal]vyberte cloudovou službu, kterou chcete aktualizovat. Tento krok otevře okno instance cloudové služby.

2. Na noži vyberte **Zaměnit**.

    ![Tlačítko Prohození cloudových služeb](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Otevře se následující výzva k potvrzení:

    ![Výměna cloudových služeb](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Po ověření informací o nasazení vyberte **OK** a zaměťte nasazení.

    K odměníní nasazení dochází rychle, protože jediné, co se mění, jsou virtuální IP adresy (VIP) pro nasazení.

    Chcete-li ušetřit výpočetní náklady, můžete odstranit pracovní nasazení po ověření, že vaše produkční nasazení funguje podle očekávání.

### <a name="common-questions-about-swapping-deployments"></a>Časté otázky týkající se výměny nasazení

**Jaké jsou předpoklady pro výměnu nasazení?**

Existují dva klíčové předpoklady pro úspěšné odkládání nasazení:

- Pokud chcete použít statickou IP adresu pro produkční slot, musíte si ji rezervovat i pro pracovní slot. V opačném případě se swap nezdaří.

- Všechny instance rolí musí být spuštěny před provedením prohození. Stav instancí můžete zkontrolovat v okně **Přehled** na portálu Azure. Případně můžete použít příkaz [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) v prostředí Windows PowerShell.

Všimněte si, že aktualizace hostovaného operačního systému a operace léčení služeb také může způsobit selhání zaměřování nasazení. Další informace naleznete [v tématu Poradce při potížích s nasazením cloudové služby](cloud-services-troubleshoot-deployment-problems.md).

**Má swap u mé aplikace prostoje? Jak bych to měl zvládnout?**

Jak je popsáno v předchozí části, odkládací nasazení je obvykle rychlé, protože je to jenom změna konfigurace v nástroji pro vyrovnávání zatížení Azure. V některých případech může trvat 10 nebo více sekund a způsobit selhání přechodného připojení. Chcete-li omezit dopad na zákazníky, zvažte implementaci [logiky opakování klienta](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Odstranění nasazení a cloudové služby
Před odstraněním cloudové služby je nutné odstranit každé existující nasazení.

Chcete-li ušetřit výpočetní náklady, můžete odstranit pracovní nasazení po ověření, že vaše produkční nasazení funguje podle očekávání. Účtují se vám výpočetní náklady pro nasazené instance rolí, které jsou zastaveny.

Pomocí následujícího postupu odstraňte nasazení nebo cloudovou službu.

1. Na [webu Azure Portal][Azure portal]vyberte cloudovou službu, kterou chcete odstranit. Tento krok otevře okno instance cloudové služby.

2. Na noži vyberte **Odstranit**.

    ![Tlačítko Odstranit cloudové služby](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Chcete-li odstranit celou cloudovou službu, zaškrtněte políčko **Cloudová služba a její nasazení.** Nebo můžete zvolit buď **produkční nasazení** nebo **pracovní nasazení** zaškrtávací políčko.

    ![Odstranění cloudových služeb](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. V dolní části vyberte **Odstranit.**

5. Chcete-li cloudovou službu odstranit, vyberte **odstranit cloudovou službu**. Potom na potvrzovací prompt, vyberte **Ano**.

> [!NOTE]
> Při odstranění cloudové služby a podrobné monitorování je nakonfigurován, je nutné odstranit data ručně z účtu úložiště. Informace o tom, kde najít tabulky metrik, najdete [v tématu Úvod do monitorování cloudových služeb](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Další informace o neúspěšných nasazeních
Okno **Přehled** má nahoře stavový řádek. Když vyberete panel, otevře se nové okno a zobrazí se všechny informace o chybě. Pokud nasazení neobsahuje žádné chyby, informační okno je prázdné.

![Přehled cloudových služeb](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Další kroky
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Přečtěte si, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [vlastního názvu domény](cloud-services-custom-domain-name-portal.md).
* Konfigurace [certifikátů TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).



