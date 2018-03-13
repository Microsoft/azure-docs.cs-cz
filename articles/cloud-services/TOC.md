# [Dokumentace ke službě Cloud Services](index.md)

# Přehled
## [Co je služba Cloud Services?](cloud-services-choose-me.md)
## [Balíčky a konfigurační soubory cloudových služeb](cloud-services-model-and-package.md)

# Začínáme
## [Příklad cloudové služby v .NET](cloud-services-dotnet-get-started.md)
## [Příklad cloudové služby v Pythonu pro Visual Studio](cloud-services-python-ptvs.md)
## [Nastavení hybridního clusteru HPC pomocí sady Microsoft HPC Pack](cloud-services-setup-hybrid-hpcpack-cluster.md)

# Postup
## Plánování
### [Velikosti virtuálních počítačů](cloud-services-sizes-specs.md)
### [Aktualizace](cloud-services-update-azure-service.md)

## Vývoj
### [tvoření rolí pracovního procesu a webu PHP](../cloud-services-php-create-web-role.md)
### [Sestavení a nasazení aplikace Node.js do Azure](cloud-services-nodejs-develop-deploy-app.md)
### [Sestavení webové aplikace Node.js pomocí Expressu](cloud-services-nodejs-develop-deploy-express-app.md)
### Služba Storage a Visual Studio
#### [Blob Storage a propojené služby](../visual-studio/vs-storage-cloud-services-getting-started-blobs.md)
#### [Queue Storage a propojené služby](../visual-studio/vs-storage-cloud-services-getting-started-queues.md)
#### [Table Storage a propojené služby](../visual-studio/vs-storage-cloud-services-getting-started-tables.md)
### [Konfigurace pravidel přenosů pro roli](cloud-services-enable-communication-role-instances.md)
### [Zpracování událostí životního cyklu cloudových služeb](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Použití Twilia k telefonování (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)

### Konfigurace úloh při spuštění
#### [Vytvoření úloh při spuštění](cloud-services-startup-tasks.md)
#### [Běžné úlohy při spuštění](cloud-services-startup-tasks-common.md)
#### [Použití úlohy pro instalaci .NET v roli cloudové služby](cloud-services-dotnet-install-dotnet.md)

### Konfigurace vzdálené plochy
#### [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
#### [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

## Nasazení
### [Vytvoření a nasazení cloudové služby na portálu](cloud-services-how-to-create-deploy-portal.md)
### [Vytvoření prázdného kontejneru cloudové služby v PowerShellu](cloud-services-powershell-create-cloud-container.md)
### [Konfigurace vlastního názvu domény](cloud-services-custom-domain-name-portal.md)
### [Připojení k vlastnímu řadiči domény](cloud-services-connect-to-custom-domain.md)

## Správa služby
### [Běžné úlohy správy](cloud-services-how-to-manage-portal.md)
### [Konfigurace cloudové služby](cloud-services-how-to-configure-portal.md)
### [Správa cloudové služby pomocí Azure Automation](automation-manage-cloud-services.md)
### [Konfigurace automatického škálování](cloud-services-how-to-scale-portal.md)
### [Použití Pythonu ke správě prostředků Azure](cloud-services-python-how-to-use-service-management.md)
### [Omezení spekulativního spouštění](mitigate-se.md)

### [Opravy hostovaného operačního systému](cloud-services-guestos-msrc-releases.md)
### Vyřazení hostovaného operačního systému
#### [Zásady vyřazení](cloud-services-guestos-retirement-policy.md)
#### [Oznámení o vyřazení řady 1 z provozu](cloud-services-guestos-family1-retirement.md)
### [Příspěvky k vydání hostovaného operačního systému](cloud-services-guestos-update-matrix.md)
### [Tahák Xpath pro konfigurace rolí Cloud Services](cloud-services-role-config-xpath.md)

## Správa certifikátů
### [Cloud Services a správa certifikátů](cloud-services-certs-create.md)
### [Konfigurace SSL](cloud-services-configure-ssl-certificate-portal.md)

## Monitorování
### [Monitorování cloudové služby](cloud-services-how-to-monitor.md)
### [Použití čítačů výkonu](diagnostics-performance-counters.md)
### [Testování výkonu](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Testování pomocí profileru sady Visual Studio](cloud-services-performance-testing-visual-studio-profiler.md)
### Povolení diagnostiky
#### [Azure PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Ukládání a zobrazení diagnostických dat v Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
### [Trasování cloudové služby pomocí diagnostiky](cloud-services-dotnet-diagnostics-trace-flow.md)

## Řešení potíží
### Ladění 
#### [Možnosti pro cloudovou službu](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Místní cloudová služba s využitím sady Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Publikovaná cloudová služba s využitím sady Visual Studio](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [Chyba přidělení cloudové služby](cloud-services-allocation-failures.md)
### [Běžné příčiny recyklování rolí cloudové služby](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [Výchozí velikost složky TEMP je pro roli příliš malá](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [Běžné problémy s nasazením](cloud-services-troubleshoot-deployment-problems.md)
### [Role se nespustila](cloud-services-troubleshoot-roles-that-fail-start.md)
### [Pokyny k zotavení](cloud-services-disaster-recovery-guidance.md)
### Cloud Services – nejčastější dotazy
#### [Dostupnost aplikací a služeb – nejčastější dotazy](cloud-services-application-and-service-availability-faq.md)
#### [Konfigurace a správa – nejčastější dotazy](cloud-services-configuration-and-management-faq.md)
#### [Možnosti připojení a sítě – nejčastější dotazy](cloud-services-connectivity-and-networking-faq.md)
#### [Nasazení – nejčastější dotazy](cloud-services-deployment-faq.md)

# Referenční informace
## [Ukázky kódu](https://azure.microsoft.com/en-us/resources/samples/?service=cloud-services)
## [Schéma .csdef XML](schema-csdef-file.md)
### [Schéma LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
### [Schéma WebRole](schema-csdef-webrole.md)
### [Schéma WorkerRole](schema-csdef-workerrole.md)
### [Schéma NetworkTrafficRules](schema-csdef-networktrafficrules.md)
## [Schéma .cscfg XML](schema-cscfg-file.md)
### [Schéma rolí](schema-cscfg-role.md)
### [Schéma NetworkConfiguration](schema-cscfg-networkconfiguration.md)
## [REST](/rest/api/compute/cloudservices/)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=compute)
## [Postup výuky](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
## [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [Ceny](https://azure.microsoft.com/pricing/details/cloud-services/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
