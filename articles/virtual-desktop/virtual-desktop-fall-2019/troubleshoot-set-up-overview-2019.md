---
title: Přehled řešení potíží s virtuálními počítači s Windows (Classic) – Azure
description: Přehled řešení potíží při nastavování klientského prostředí pro virtuální počítače s Windows (Classic)
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7b2220ffe92446338e1b9db9b329847d93830334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88005427"
---
#  <a name="windows-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Přehled řešení potíží s virtuálními počítači s Windows (Classic), zpětná vazba a podpora

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../troubleshoot-set-up-overview.md).

Tento článek poskytuje přehled o problémech, se kterými se můžete setkat při nastavování prostředí tenanta virtuálních počítačů s Windows, a poskytuje způsoby, jak tyto problémy vyřešit.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="escalation-tracks"></a>Cvičení eskalace

Pomocí následující tabulky můžete identifikovat a vyřešit problémy, se kterými se můžete setkat při nastavování prostředí klienta pomocí klienta vzdálené plochy. Po nastavení vašeho tenanta můžete využít naši novou [službu diagnostiky](diagnostics-role-service-2019.md) k identifikaci problémů pro běžné scénáře.

>[!NOTE]
> Máme Fórum odborné komunity, které můžete navštívit a diskutovat o problémech s produktovým týmem a aktivními členy komunity. Pokud chcete začít diskuzi, navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) .

| **Problém**                                                            | **Navrhované řešení**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Vytváření tenanta virtuálních klientů Windows                                                    | Pokud dojde k výpadku Azure, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/). v opačném případě [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu, vyberte **nasazení** pro typ problému a pak vyberte **problémy s vytvářením tenanta virtuálních klientů Windows** pro problémový typ problému.|
| Přístup k šablonám Marketplace v Azure Portal       | Pokud dojde k výpadku Azure, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Azure Marketplace šablony virtuálních klientů Windows jsou volně dostupné.|
| Přístup k šablonám Azure Resource Manager z GitHubu                                  | Přečtěte si část [Vytvoření virtuálních počítačů hostitele relace virtuálních počítačů s Windows](troubleshoot-set-up-issues-2019.md#creating-windows-virtual-desktop-session-host-vms) v tématu [Vytvoření fondu hostitelů a vytváření fondů hostitelů](troubleshoot-set-up-issues-2019.md). Pokud je problém stále nevyřešený, obraťte se na [tým podpory GitHubu](https://github.com/contact). <br> <br> Pokud k této chybě dojde po přístupu k šabloně v GitHubu, obraťte se na [podporu Azure](https://azure.microsoft.com/support/create-ticket/).|
| Fond hostitelů relace – Azure Virtual Network (VNET) a nastavení expresní trasy               | [Otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/)a pak vyberte příslušnou službu (v kategorii síť). |
| Vytvoření virtuálního počítače fondu hostitelů relace, když se nepoužívají šablony Azure Resource Manager s virtuálním počítačem s Windows | [Otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/)a pak pro tuto službu vyberte **virtuální počítač s Windows** . <br> <br> Problémy s Azure Resource Manager šablonami, které jsou k dispozici s virtuálním počítačem s Windows, najdete v části Vytvoření tenanta virtuálních počítačů s Windows v tématu vytváření [fondů klientů a fondů hostitelů](troubleshoot-set-up-issues-2019.md). |
| Správa prostředí hostitele relace virtuálních počítačů s Windows z Azure Portal    | [Otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Problémy se správou při používání PowerShellu služby Vzdálená plocha/prostředí Windows Virtual Desktop PowerShell najdete v tématu virtuální plocha [Windows PowerShell](troubleshoot-powershell-2019.md) nebo [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu, vyberte **Konfigurace a Správa** pro typ problému a pak vyberte **problémy s konfigurací tenanta pomocí PowerShellu** pro problémový podtyp. |
| Správa konfigurace virtuálních klientských počítačů s Windows vázaných na fondy hostitelů a skupiny aplikací (skupiny aplikací)      | Podívejte se na téma [PowerShell virtuální plochy Windows](troubleshoot-powershell-2019.md)nebo [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu a potom vyberte vhodný typ problému.|
| Nasazení a Správa kontejnerů profilů FSLogix | Přečtěte si [příručku Poradce při potížích s produkty FSLogix](/fslogix/fslogix-trouble-shooting-ht/) . Pokud se problém nevyřeší, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu, pro typ problému vyberte **FSLogix** a pak vyberte příslušný podtyp problému. |
| Selhání klientů vzdálené plochy při spuštění                                                 | Podívejte [se na téma Poradce při potížích s klientem vzdálené plochy](../troubleshoot-client.md) a v případě, že problém nevyřeší,  [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu a pak jako typ problému vyberte **Klienti vzdálené plochy** .  <br> <br> Pokud se jedná o problém v síti, uživatelé musí kontaktovat svého správce sítě. |
| Připojeno, ale bez kanálu                                                                 | Řešení potíží pomocí nástroje se [připojuje, ale nic se nezobrazuje (bez informačního kanálu)](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) v [připojeních služby Virtual Desktop systému Windows](troubleshoot-service-connection-2019.md). <br> <br> Pokud byli vaši uživatelé přiřazeni ke skupině aplikací,  [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu a pak jako typ problému vyberte **Klienti vzdálené plochy** . |
| Podávat potíže se zjišťováním v důsledku sítě                                            | Uživatelé musí kontaktovat svého správce sítě. |
| Připojování klientů                                                                    | Viz [připojení k virtuálním počítačům s Windows](troubleshoot-service-connection-2019.md) a pokud to problém nevyřeší, přečtěte si téma [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration-2019.md). |
| Reakce vzdálených aplikací nebo plochy                                      | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |
| Licenční zprávy a chyby                                                          | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |
| Problémy při používání nástrojů virtuálních klientů Windows na GitHubu (šablony Azure Resource Manager, nástroj pro diagnostiku, nástroj pro správu) | Další informace najdete v tématu [šablony Azure Resource Manager pro službu Vzdálená plocha](https://github.com/Azure/RDS-Templates/blob/master/README.md) k nahlášení problémů. |

## <a name="next-steps"></a>Další kroky

- Pokud chcete řešit problémy při vytváření tenanta a fondu hostitelů v prostředí virtuálních počítačů s Windows, přečtěte si téma [vytváření fondů klientů a hostitelů](troubleshoot-set-up-issues-2019.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration-2019.md).
- Informace o řešení potíží s klientskými připojeními k virtuální ploše Windows najdete v tématu [připojení ke službě Virtual Desktop systému Windows](troubleshoot-service-connection-2019.md).
- Řešení potíží s klienty vzdálené plochy najdete v tématu [řešení potíží s klientem vzdálené plochy](../troubleshoot-client.md) .
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell-2019.md).
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup-2019.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../../azure-resource-manager/management/view-activity-logs.md).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](../../azure-resource-manager/templates/deployment-history.md).
