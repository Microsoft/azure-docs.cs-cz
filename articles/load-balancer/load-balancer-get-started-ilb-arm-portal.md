---
title: Vytvoření interního nástroje pro vyrovnávání zatížení – Azure Portal | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit interní nástroj pro vyrovnávání zatížení v Resource Manageru pomocí webu Azure Portal
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
tags: azure-service-management
ms.assetid: 1ac14fb9-8d14-4892-bfe6-8bc74c48ae2c
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4cb61b60f97238a3c34953c679f177ef16056092
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Vytvoření interního nástroje pro vyrovnávání zatížení na webu Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Šablona](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Začínáme vytvářet interní nástroj pro vyrovnávání zatížení pomocí webu Azure Portal

Pomocí následujícího postupu vytvořte interní nástroj pro vyrovnávání zatížení z webu Azure Portal.

1. Otevřete prohlížeč, přejděte na web [Azure Portal](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.
3. V okně **Vytvořit nástroj pro vyrovnávání zatížení** zadejte **Název** svého nástroje pro vyrovnávání zatížení.
4. V části **Typ** klikněte na **Interní**.
5. Klikněte na **Virtuální síť** a potom vyberte virtuální síť, ve které chcete vytvořit nástroj pro vyrovnávání zatížení.

   > [!NOTE]
   > Pokud nevidíte virtuální síť, kterou chcete použít, zkontrolujte **Umístění**, které používáte pro nástroj pro vyrovnávání zatížení, a odpovídajícím způsobem jej změňte.

6. Klikněte na **Podsíť** a potom vyberte podsíť, ve které chcete vytvořit nástroj pro vyrovnávání zatížení.
7. V části **Přiřazení IP adresy** klikněte buď na **Dynamické**, nebo **Statické** v závislosti na tom, jestli chcete, aby IP adresa nástroje pro vyrovnávání zatížení byla pevná (statická) nebo ne.

   > [!NOTE]
   > Pokud vyberete použití statické IP adresy, musíte zadat adresu nástroje pro vyrovnávání zatížení.

8. V části **Skupina prostředků** buď zadejte název nové skupiny prostředků pro nástroj pro vyrovnávání zatížení, nebo klikněte na **vybrat existující** a vyberte existující skupinu prostředků.
9. Klikněte na možnost **Vytvořit**.

## <a name="configure-load-balancing-rules"></a>Konfigurace pravidel vyrovnávání zatížení

Po vytvoření nástroje pro vyrovnávání zatížení přejděte do prostředku nástroje pro vyrovnávání zatížení, abyste jej nakonfigurovali.
Před konfigurací pravidla vyrovnávání zatížení nakonfigurujte back-endový fond adres a test.

### <a name="step-1-configure-a-backend-pool"></a>Krok 1: Konfigurace back-endového fondu

1. Na webu Azure Portal klikněte na **Procházet** > **Nástroje pro vyrovnávání zatížení** a potom klikněte na nástroj pro vyrovnávání zatížení, který jste vytvořili dříve.
2. Na stránce **Nastavení** klikněte na **Back-endové fondy**.
3. Na stránce **Back-endové fondy adres** klikněte na **Přidat**.
4. Na stránce **Přidat back-endový fond** zadejte **Název** back-endového fondu a potom klikněte na **OK**.

### <a name="step-2-configure-a-probe"></a>Krok 2: Konfigurace testu

1. Na webu Azure Portal klikněte na **Procházet** > **Nástroje pro vyrovnávání zatížení** a potom klikněte na nástroj pro vyrovnávání zatížení, který jste vytvořili dříve.
2. Na stránce **Nastavení** klikněte na **Sondy stavu**.
3. Na stránce **Sondy stavu** klikněte na **Přidat**.
4. Na stránce **Přidat sondu stavu** zadejte **Název** sondy.
5. V části **Protokol** vyberte **HTTP** (pro weby) nebo **TCP** (pro ostatní aplikace založené na protokolu TCP).
6. V části **Port** zadejte port, který se má použít při přístupu k testu.
7. V části **Cesta** (pouze pro testy HTTP) zadejte cestu, která se má použít jako test.
8. V části **Interval** zadejte, jak často se má aplikace testovat.
9. V části **Prahová hodnota špatného stavu** zadejte, kolik pokusů musí selhat, než je back-endový virtuální počítač označen jako Není v pořádku.
10. Kliknutím na **OK** vytvořte test.

### <a name="step-3-configure-load-balancing-rules"></a>Krok 3: Konfigurace pravidel vyrovnávání zatížení

1. Na webu Azure Portal klikněte na **Procházet** > **Nástroje pro vyrovnávání zatížení** a potom klikněte na nástroj pro vyrovnávání zatížení, který jste vytvořili dříve.
2. Na stránce **Nastavení** klikněte na **Pravidla vyrovnávání zatížení**.
3. Na stránce **Pravidla vyrovnávání zatížení** klikněte na **Přidat**.
4. Na stránce **Přidat pravidlo vyrovnávání zatížení** zadejte **Název** pravidla.
5. V části **Protokol** vyberte **TCP** nebo **UDP**.
6. V části **Port** zadejte port, ke kterému se připojují klienti v nástroji pro vyrovnávání zatížení.
7. V části **Back-endový port** zadejte port, který se má použít v back-endovém fondu (port nástroje pro vyrovnávání zatížení a back-endový port jsou obvykle stejné).
8. V části **Back-endový fond** vyberte back-endový fond, který jste vytvořili dříve.
9. V části **Trvalost relace** vyberte, jak chcete uchovávat relace.
10. V části **Časový limit nečinnosti (v minutách)** zadejte časový limit nečinnosti.
11. V části **Plovoucí IP (přímá odpověď ze serveru)** klikněte na **Zakázáno** nebo **Povoleno**.
12. Klikněte na **OK**.

## <a name="next-steps"></a>Další kroky

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)

