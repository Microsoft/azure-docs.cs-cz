---
title: Přehled řešení potíží s virtuálním počítačem s Windows – Azure
description: Přehled řešení potíží při nastavování prostředí klienta virtuální plochy Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/06/2019
ms.author: helohr
ms.openlocfilehash: ce1af278d67f96873ed611db8dc05938205638dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980245"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Přehled řešení potíží, zpětná vazba a podpora

Tento článek poskytuje přehled o problémech, se kterými se můžete setkat při nastavování prostředí tenanta virtuálních počítačů s Windows, a poskytuje způsoby, jak tyto problémy vyřešit.

## <a name="provide-feedback"></a>Poskytnout zpětnou vazbu

Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="escalation-tracks"></a>Cvičení eskalace

Pomocí následující tabulky můžete identifikovat a vyřešit problémy, se kterými se můžete setkat při nastavování prostředí klienta pomocí klienta vzdálené plochy. Po nastavení vašeho tenanta můžete využít naši novou [službu diagnostiky](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) k identifikaci problémů pro běžné scénáře.

>[!NOTE]
> Máme Fórum odborné komunity, které můžete navštívit a diskutovat o problémech s produktovým týmem a aktivními členy komunity. Navštivte [technickou komunitu virtuálních počítačů s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) a

| **Problém**                                                            | **Navrhované řešení**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Vytváření tenanta                                                    | Pokud dojde k výpadku Azure, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/). jinak **otevřete žádost o podporu pro virtuální počítač s Windows (COMPUTE)** .|
| Přístup k šablonám Marketplace v Azure Portal       | Pokud dojde k výpadku Azure, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/). <br> <br> Azure Marketplace šablony virtuálních klientů Windows jsou volně dostupné.|
| Přístup k šablonám Azure Resource Manager z GitHubu                                  | Přečtěte si část vytvoření virtuálních počítačů hostitele relace virtuálních počítačů s Windows v tématu vytvoření [fondu hostitelů a vytváření fondů hostitelů](troubleshoot-set-up-issues.md). Pokud je problém stále nevyřešený, obraťte se na [tým podpory GitHubu](https://github.com/contact). <br> <br> Pokud k této chybě dojde po přístupu k šabloně v GitHubu, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/).|
| Fond hostitelů relace – Azure Virtual Network (VNET) a nastavení expresní trasy               | Kontaktujte **podporu Azure (sítě)** . |
| Vytvoření virtuálního počítače fondu hostitelů relace, když se nepoužívají šablony Azure Resource Manager s virtuálním počítačem s Windows | Kontaktujte **podporu Azure (COMPUTE)** . <br> <br> Problémy s Azure Resource Manager šablonami, které jsou k dispozici s virtuálním počítačem s Windows, najdete v části Vytvoření tenanta virtuálních počítačů s Windows v tématu vytváření [fondů klientů a fondů hostitelů](troubleshoot-set-up-issues.md). |
| Správa prostředí hostitele relace virtuálních počítačů s Windows z Azure Portal    | Kontaktujte **podporu Azure**. <br> <br> Problémy se správou při používání PowerShellu služby Vzdálená plocha/prostředí PowerShell pro virtuální počítače s Windows najdete v tématu [virtuální plocha Windows PowerShell](troubleshoot-powershell.md) nebo **otevřete žádost o podporu pro virtuální plochu Windows (COMPUTE)** . |
| Správa konfigurace virtuálních klientských počítačů s Windows vázaných na fondy hostitelů a skupiny aplikací (skupiny aplikací)      | Podívejte se na téma [PowerShell virtuální plochy Windows](troubleshoot-powershell.md)nebo **otevřete žádost o podporu pro virtuální plochu Windows (COMPUTE)** . <br> <br> Pokud jsou problémy svázané s ukázkovým grafickým uživatelským rozhraním (GUI), můžete se obrátit na komunitu Yammer.|
| Selhání klientů vzdálené plochy při spuštění                                                 | Podívejte [se na téma Poradce při potížích s klientem vzdálené plochy](troubleshoot-client.md) a v případě, že problém nevyřeší, **otevřete žádost o podporu pro virtuální plochu Windows (COMPUTE)** .  <br> <br> Pokud se jedná o problém v síti, uživatelé musí kontaktovat svého správce sítě. |
| Připojeno, ale bez kanálu                                                                 | Řešení potíží pomocí "uživatel se připojuje, ale nic se nezobrazuje (bez informačního kanálu)" [řešení potíží s klientem vzdálené plochy](troubleshoot-client.md). <br> <br> Pokud byli vaši uživatelé přiřazeni ke skupině aplikací, **otevřete žádost o podporu pro virtuální plochu Windows (COMPUTE)** . |
| Podávat potíže se zjišťováním v důsledku sítě                                            | Uživatelé musí kontaktovat svého správce sítě. |
| Připojování klientů                                                                    | Viz [připojení k virtuálním počítačům s Windows](troubleshoot-service-connection.md) a pokud to problém nevyřeší, přečtěte si téma [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md). |
| Reakce vzdálených aplikací nebo plochy                                      | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |
| Licenční zprávy a chyby                                                          | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |
| Problémy při používání nástrojů virtuálních klientů Windows na GitHubu (šablony Azure Resource Manager, nástroj pro diagnostiku, nástroj pro správu) | Další informace najdete v tématu [šablony Azure Resource Manager pro službu Vzdálená plocha](https://github.com/Azure/RDS-Templates/blob/master/README.md) k nahlášení problémů. |

## <a name="next-steps"></a>Další kroky

- Pokud chcete řešit problémy při vytváření tenanta a fondu hostitelů v prostředí virtuálních počítačů s Windows, přečtěte si téma [vytváření fondů klientů a hostitelů](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení potíží s klientskými připojeními k virtuální ploše Windows najdete v tématu [připojení ke službě Virtual Desktop systému Windows](troubleshoot-service-connection.md).
- Řešení potíží s klienty vzdálené plochy najdete v tématu [řešení potíží s klientem vzdálené plochy](troubleshoot-client.md) .
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
