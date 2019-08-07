---
title: Přehled řešení potíží s virtuálním počítačem s Windows, názory a podpora – Azure
description: Přehled řešení potíží při nastavování prostředí klienta virtuální plochy Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: e652c8a67db686159a2c3fd48e2ea65888bcc10d
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816347"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Přehled řešení potíží, zpětná vazba a podpora

Tento článek poskytuje přehled o problémech, se kterými se můžete setkat při nastavování prostředí tenanta virtuálních počítačů s Windows, a poskytuje způsoby, jak tyto problémy vyřešit.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

V současné době nepřijímáme případy podpory, ale virtuální počítač s Windows je ve verzi Preview. Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="escalation-tracks"></a>Cvičení eskalace

Pomocí následující tabulky můžete identifikovat a vyřešit problémy, se kterými se můžete setkat při nastavování prostředí klienta pomocí klienta vzdálené plochy. Po nastavení vašeho tenanta můžete využít naši novou [službu diagnostiky](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) k identifikaci problémů pro běžné scénáře.

>[!NOTE]
>V současné době nepřijímáme případy podpory, ale virtuální počítač s Windows je ve verzi Preview. Kdykoli budeme odkazovat na podporu virtuálních počítačů s Windows, přejděte k našemu komunitnímu fóru pro naši technickou komunitu. Navštivte [technickou komunitu virtuálních počítačů s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o problémech s produktovým týmem a aktivními členy komunity. Pokud potřebujete vyřešit problém podpory, uveďte ID aktivity a přibližný časový rámec, kdy k problému došlo.

| **Chybu**                                                            | **Navrhované řešení**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Vytváření tenanta                                                    | Pokud dojde k výpadku Azure, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/). v opačném případě kontaktujte **podporu služby Vzdálená plocha/virtuálního počítače s Windows**.|
| Přístup k šablonám Marketplace v Azure Portal       | Pokud dojde k výpadku Azure, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/). <br> <br> Azure Marketplace šablony virtuálních klientů Windows jsou volně dostupné.|
| Přístup k šablonám Azure Resource Manager z GitHubu                                  | Přečtěte si část vytvoření virtuálních počítačů hostitele relace virtuálních počítačů s Windows v tématu vytvoření [fondu hostitelů a vytváření fondů hostitelů](troubleshoot-set-up-issues.md). Pokud je problém stále nevyřešený, obraťte se na [tým podpory GitHubu](https://github.com/contact). <br> <br> Pokud k této chybě dojde po přístupu k šabloně v GitHubu, obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/).|
| Fond hostitelů relace – Azure Virtual Network (VNET) a nastavení expresní trasy               | Kontaktujte **podporu Azure (sítě)** . |
| Vytvoření virtuálního počítače fondu hostitelů relace, když se nepoužívají šablony Azure Resource Manager s virtuálním počítačem s Windows | Kontaktujte **podporu Azure (COMPUTE)** . <br> <br> Problémy s Azure Resource Manager šablonami, které jsou k dispozici s virtuálním počítačem s Windows, najdete v části Vytvoření tenanta virtuálních počítačů s Windows v tématu vytváření [fondů klientů a fondů hostitelů](troubleshoot-set-up-issues.md). |
| Správa prostředí hostitele relace virtuálních počítačů s Windows z Azure Portal    | Kontaktujte **podporu Azure**. <br> <br> Problémy se správou při používání PowerShellu služby Vzdálená plocha/prostředí PowerShell pro virtuální počítače s Windows najdete v tématu [prostředí PowerShell pro virtuální počítače s Windows](troubleshoot-powershell.md) nebo se obraťte na **tým podpory služby Vzdálená plocha/Windows pro virtuální počítače**. |
| Správa konfigurace virtuálních klientských počítačů s Windows vázaných na fondy hostitelů a skupiny aplikací (skupiny aplikací)      | Podívejte se na téma [prostředí PowerShell pro virtuální plochu systému Windows](troubleshoot-powershell.md)nebo se obraťte na **tým podpory služby Vzdálená plocha/Windows Virtual Desktop**. <br> <br> Pokud jsou problémy svázané s ukázkovým grafickým uživatelským rozhraním (GUI), můžete se obrátit na komunitu Yammer.|
| Selhání klientů vzdálené plochy při spuštění                                                 | Viz [připojení klienta vzdálené plochy](troubleshoot-client-connection.md) . Pokud to neřeší, obraťte se na **tým podpory služby Vzdálená plocha/Windows Virtual Desktop**.  <br> <br> Pokud se jedná o problém v síti, uživatelé musí kontaktovat svého správce sítě. |
| Připojeno, ale bez kanálu                                                                 | Řešení potíží s použitím "připojovat uživatele", ale nic se nezobrazuje (bez informačního kanálu) oddílu [připojení klienta vzdálené plochy](troubleshoot-client-connection.md). <br> <br> Pokud byli vaši uživatelé přiřazeni ke skupině aplikací, přesměrujte na **tým podpory služby Vzdálená plocha/Windows Virtual Desktop**. |
| Podávat potíže se zjišťováním v důsledku sítě                                            | Uživatelé musí kontaktovat svého správce sítě. |
| Připojování klientů                                                                    | Viz [připojení klientů vzdálené plochy](troubleshoot-client-connection.md) a pokud problém nevyřeší, přečtěte si téma [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md). |
| Reakce vzdálených aplikací nebo plochy                                      | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |
| Licenční zprávy a chyby                                                          | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |

## <a name="next-steps"></a>Další postup

- Pokud chcete řešit problémy při vytváření tenanta a fondu hostitelů v prostředí virtuálních počítačů s Windows, přečtěte si téma [vytváření fondů klientů a hostitelů](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení problémů s připojením klienta k virtuální ploše Windows najdete v tématu [připojení klientů vzdálené plochy](troubleshoot-client-connection.md).
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Další informace o službě verze Preview najdete v tématu [prostředí Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Postup při řešení potíží najdete v [kurzu: Řešení potíží s nasazeními](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)šablon Správce prostředků.
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).