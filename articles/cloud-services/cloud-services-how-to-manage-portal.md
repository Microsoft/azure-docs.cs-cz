---
title: Běžné úlohy správy cloudové služby | Microsoft Docs
description: Naučte se spravovat cloudové služby na portálu Azure. Tyto příklady použití portálu Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: e60bf5c82e68d49abaa44d80ac9fafba2d8265da
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29121191"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Spravovat cloudové služby na portálu Azure
V **cloudové služby** oblasti portálu Azure, můžete:

* Aktualizujte roli služby nebo nasazení.
* Umožňuje zvýšit úroveň dvoufázového nasazení do produkčního prostředí.
* Propojte prostředky ke cloudové službě, můžete zobrazit závislosti prostředků a škálování prostředky společně.
* Odstraňte cloudovou službu nebo nasazení.

Další informace o tom, jak škálování cloudové služby najdete v tématu [nakonfigurovat automatické škálování pro cloudové služby na portálu](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Aktualizace nasazení nebo role cloudové služby
Pokud je potřeba aktualizovat kód aplikace pro cloudové služby, použijte **aktualizace** v okně cloudové služby. Můžete aktualizovat roli jednoho nebo všech rolí. Pokud chcete aktualizovat, můžete nahrát nový balíček služby nebo konfigurační soubor služby.

1. V [portál Azure][Azure portal], vyberte cloudovou službu, kterou chcete aktualizovat. Tento krok se otevře okno instance služby pro cloud.

2. V okně vyberte **aktualizace**.

    ![Tlačítko Aktualizovat](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Aktualizujte nasazení nového souboru balíku služeb (.cspkg) a konfigurační soubor služby (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Volitelně můžete Aktualizujte účet úložiště a označení nasazení.

5. Pokud žádné role mít pouze jednu instanci role, vyberte **nasadit, i když jedna nebo víc rolí obsahuje jednu instanci** zaškrtávacího políčka umožníte upgradu pokračovat.

    Azure může zaručit pouze 99,95 % dostupnost služby při aktualizaci cloudové služby, pokud každá role má aspoň dvě instance role (virtuální počítače). Se dvěma instancemi role jeden virtuální počítač zpracovává požadavky na klienta během dalších je aktualizována.

6. Vyberte **spuštění nasazení** zaškrtávací políčko k použití aktualizace po dokončení nahrávání balíčku.

7. Vyberte **OK** spustíte aktualizaci služby.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Swap – nasazení na podporu dvoufázového nasazení do produkčního prostředí
Pokud se rozhodnete nasadit novou verzi cloudové služby, fáze a otestovat novou verzi v prostředí s pracovní cloudové služby. Použití **Prohodit** přepnout adresy URL, které dvě nasazení se podrobněji a povyšte novou verzí do produkčního prostředí.

Můžete zaměnit nasazení z **cloudové služby** stránku nebo řídicí panel.

1. V [portál Azure][Azure portal], vyberte cloudovou službu, kterou chcete aktualizovat. Tento krok se otevře okno instance služby pro cloud.

2. V okně vyberte **odkládacího souboru**.

    ![Cloudové služby Prohodit tlačítko](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Otevře se následující výzvu k potvrzení:

    ![Cloudové služby Swap](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Po ověření, informace o nasazení, vyberte **OK** chcete Prohodit nasazení.

    Swap nasazení dochází rychle jediné, co se změní je virtuální IP adresy (VIP) pro nasazení.

    Pokud chcete uložit výpočetní náklady, můžete odstranit pracovní nasazení po ověření, že produkčního nasazení funguje podle očekávání.

### <a name="common-questions-about-swapping-deployments"></a>Časté otázky týkající se nasazení vzájemná záměna

**Jaké jsou požadavky pro odkládací nasazení?**

Existují dva klíče požadavky pro úspěšné nasazení prohození:

- Pokud chcete používat statickou IP adresu pro vaše produkční slot, jeden pro přípravný slot také musíte rezervovat. Prohození, jinak selže.

- Všechny instance role musí být spuštěna, než bude možné provést prohození. Můžete zkontrolovat stav vaší instance **přehled** okno portálu Azure. Alternativně můžete použít [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) příkazu v prostředí Windows PowerShell.

Poznámka: aktualizace operačního systému hosta a službou opravy operations také způsobit nasazení záměna selhání. Další informace najdete v tématu [Poradce při potížích se nasazení služby cloud](cloud-services-troubleshoot-deployment-problems.md).

**Nesnižuje prohození výpadek své aplikaci? Jak by měly zpracovávat ho?**

Jak je popsáno v předchozí části, je obvykle rychlé prohození nasazení, protože je právě změnu konfigurace pro vyrovnávání zatížení Azure. V některých případech může trvat 10 nebo další sekund a výsledek selhání přechodný připojení. Chcete-li omezit vliv na vaše zákazníky, zvažte implementaci [logika opakovaných pokusů klienta](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Odstranění nasazení a cloudové služby
Před odstraněním cloudové služby, je nutné odstranit každé existující nasazení.

Pokud chcete uložit výpočetní náklady, můžete odstranit pracovní nasazení po ověření, že produkčního nasazení funguje podle očekávání. Fakturuje se výpočetní náklady pro instance nasazené rolí, které jsou zastaveny.

Pomocí následujícího postupu můžete odstranit nasazení nebo cloudové služby.

1. V [portál Azure][Azure portal], vyberte cloudovou službu, kterou chcete odstranit. Tento krok se otevře okno instance služby pro cloud.

2. V okně vyberte **odstranit**.

    ![Cloud Services odstranit tlačítko](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Pokud chcete odstranit celou cloudové služby, vyberte **Cloudová služba a její nasazení** zaškrtávací políčko. Nebo můžete zvolit buď **produkční nasazení** nebo **pracovní nasazení** zaškrtávací políčko.

    ![Cloud Services Delete](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Vyberte **odstranit** dole.

5. Chcete-li odstranit cloudové služby, vyberte **odstranění Cloudová služba**. Potom v potvrzovací výzvu, vyberte **Ano**.

> [!NOTE]
> Pokud Cloudová služba je Odstraněná a podrobné monitorování je nastavena, musíte odstranit data ručně z vašeho účtu úložiště. Informace o tom, kde najít metriky tabulky najdete v tématu [Úvod do cloudu monitorování služby](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Další informace o selhání nasazení
**Přehled** okno obsahuje stavového řádku v horní části. Po výběru panelu nové okno se zobrazí veškeré informace o chybě. Pokud nasazení neobsahuje žádné chyby, v okně informace je prázdný.

![Přehled služby cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Další postup
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Zjistěte, jak [nasazení cloudové služby](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [vlastní název domény](cloud-services-custom-domain-name-portal.md).
* Konfigurace [certifikáty SSL](cloud-services-configure-ssl-certificate-portal.md).
