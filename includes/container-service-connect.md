# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>Vzdálené připojení ke clusteru Kubernetes, DC/OS nebo Docker Swarm
Po vytvoření clusteru Azure Container Service je nutné se ke clusteru připojit kvůli nasazení a správě úloh. Tento článek popisuje, jak se připojit k hlavnímu virtuálnímu počítači clusteru ze vzdáleného počítače. 

Clustery Kubernetes, DC/OS a Docker Swarm poskytují koncové body HTTP místně. V případě Kubernetes je tento koncový bod bezpečně zpřístupněn na internetu a můžete se k němu přímo připojit z libovolného počítače připojeného k internetu spuštěním pomocí nástroje `kubectl` z příkazového řádku. 

V případě DC/OS a Docker Swarm doporučujeme z místního počítače k systému pro správu clusteru vytvořit tunel Secure Shell (SSH). Po vytvoření tunelu můžete spouštět příkazy, které využívají koncové body HTTP, a zobrazovat webové rozhraní orchestrátoru (pokud je k dispozici) z místního systému. 

## <a name="prerequisites"></a>Požadavky

* Cluster Kubernetes, DC/OS nebo Docker Swarm [nasazený ve službě Azure Container Service](../articles/container-service/dcos-swarm/container-service-deployment.md).
* Privátní klíč SSH RSA odpovídající veřejnému klíči SSH přidaného do clusteru během nasazení Tyto příkazy předpokládají, že privátní klíč SSH je ve vašem počítači ve složce `$HOME/.ssh/id_rsa`. Tyto pokyny existují také pro [macOS a Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) nebo pro [Windows](../articles/virtual-machines/linux/ssh-from-windows.md). Pokud připojení SSH nefunguje, možná budete muset [resetovat vaše klíče SSH](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Připojení ke clusteru Kubernetes

Podle následujícího postupu nainstalujete a nakonfigurujete `kubectl` ve vašem počítači.

> [!NOTE] 
> V Linuxu nebo v macOS může být nutné spouštět uvedené příkazy pomocí `sudo`.
> 

### <a name="install-kubectl"></a>Instalace kubectl
Jedním ze způsobů, jak tento nástroj nainstalovat, je použít nástroj příkazového řádku Azure 2.0 `az acs kubernetes install-cli`. Pokud chcete spustit tento příkaz, ujistěte se, že jste [nainstalován](/cli/azure/install-az-cli2) nejnovější Azure CLI 2.0 a přihlášeného k účtu Azure (`az login`).

```azurecli
# Linux or macOS
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Případně si můžete nejnovějšího klienta `kubectl` stáhnout přímo ze [stránky vydaných verzí Kubernetes](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md). Další informace najdete v tématu [Instalace a nastavení kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).

### <a name="download-cluster-credentials"></a>Stažení přihlašovacích údajů clusteru
Jakmile budete mít `kubectl` nainstalovaný, je třeba, abyste na svůj počítač zkopírovali přihlašovací údaje clusteru. Přihlašovací údaje můžete získat například příkazem `az acs kubernetes get-credentials`. Předejte název skupiny prostředků a název prostředku kontejnerové služby:

```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Tento příkaz stáhne přihlašovací údaje clusteru do složky `$HOME/.kube/config`, kde `kubectl` očekává, že je najde.

Případně můžete použít `scp` a bezpečně zkopírovat soubor ze složky `$HOME/.kube/config` na hlavním virtuálním počítači do svého místního počítače. Příklad:

```bash
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

V systému Windows můžete použít Bash on Ubuntu on Windows, klienta pro bezpečné kopírování souborů PuTTY nebo podobný nástroj.

### <a name="use-kubectl"></a>Použití kubectl

Jakmile bude `kubectl` nakonfigurovaný, otestujte připojení výpisem uzlů ve vašem clusteru:

```bash
kubectl get nodes
```

Můžete vyzkoušet i další příkazy nástroje `kubectl`. Můžete například zobrazit řídicí panel Kubernetes. Nejprve spusťte proxy na serveru Kubernetes API:

```bash
kubectl proxy
```

Uživatelské rozhraní Kubernetes je nyní k dispozici na adrese: `http://localhost:8001/ui`.

Další informace najdete v tématu [Rychlé představení Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Připojení ke clusteru DC/OS nebo Swarm

Pokud chcete používat clustery DC/OS a Docker Swarm nasazené ve službě Azure Container Service, postupujte podle těchto pokynů a vytvořte tunel SSH z místního systému Linux, macOS nebo Windows. 

> [!NOTE]
> Tyto pokyny se zaměřují na tunelování přenosů TCP přes protokol SSH. Můžete také spustit interaktivní relaci SSH s jedním z interních systémů pro správu clusteru, ale nedoporučuje se to. Práce přímo v interním systému s sebou nese riziko neúmyslných změn konfigurace.  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-macos"></a>Vytvoření tunelu SSH v Linuxu a macOS
První věc, kterou je nutné udělat, když vytváříte tunel SSH v Linuxu nebo macOS, je nalezení veřejného názvu DNS hlavních serverů s vyrovnáváním zatížení. Postupujte následovně:


1. Na webu [Azure Portal](https://portal.azure.com) přejděte do skupiny prostředků obsahující váš cluster kontejnerové služby. Rozbalte skupinu prostředků, aby se zobrazily všechny prostředky. 

2. Klikněte na prostředek **Služba kontejneru** a potom klikněte na **Přehled**. V části **Základy** se zobrazí **Hlavní plně kvalifikovaný název domény** clusteru. Uložte si tento název pro pozdější použití. 

    ![Veřejný název DNS](./media/container-service-connect/pubdns.png)

    Další možností je spustit příkaz `az acs show` pro kontejnerovou službu. Ve výstupu tohoto příkazu vyhledejte vlastnost **Master Profile:fqdn**.

3. Nyní otevřete prostředí shell a spusťte příkaz `ssh` s následujícími parametry: 

    **LOCAL_PORT** je port TCP na straně služby tunelu, ke kterému se chcete připojit. Pro Swarm nastavte tuto hodnotu na 2375. Pro DC/OS nastavte tuto hodnotu na 80. 
    **REMOTE_PORT** je port koncového bodu, který chcete zveřejnit. Pro Swarm použijte 2375. Pro DC/OS použijte port 80.  
    **USERNAME** je uživatelské jméno zadané ve chvíli, kdy jste nasadili cluster.  
    **DNSPREFIX** je předpona DNS zadaná ve chvíli, kdy jste nasadili cluster.  
    **REGION** je oblast, ve které je umístěna skupina prostředků.  
    **PATH_TO_PRIVATE_KEY** [NEPOVINNÉ] je cesta k privátnímu klíči, který odpovídá veřejnému klíči zadanému při vytváření clusteru. Tuto možnost použijte spolu s příznakem `-i`.

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com
    ```
  
  > [!NOTE]
  > Port pro připojení SSH je 2200, nikoli standardní port 22. V clusteru s více hlavními virtuálními počítači je to port pro připojení k prvnímu hlavnímu virtuálnímu počítači.
  > 

  Příkaz nevrací žádný výstup.

Podívejte se na příklady pro DC/OS a Swarm v následujících částech.    

### <a name="dcos-tunnel"></a>Tunel DC/OS
Pokud chcete otevřít tunel pro koncové body DC/OS, spusťte příkaz podobný následujícímu příkazu:

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> Ujistěte se, že se na port 80 neváže žádný jiný místní proces. V případě potřeby můžete zadat jiný místní port než 80, třeba port 8080. Při použití tohoto portu ale některé odkazy webového uživatelského rozhraní nemusí fungovat.
>

Nyní máte ke koncovým bodům DC/OS přístup z místního systému prostřednictvím následujících adres URL (předpokládá se místní port 80):

* DC/OS: `http://localhost:80/`
* Marathon: `http://localhost:80/marathon`
* Mesos: `http://localhost:80/mesos`

Obdobně můžete přes tento tunel kontaktovat rozhraní REST API pro každou z aplikací.

### <a name="swarm-tunnel"></a>Tunel Swarm
Pokud chcete otevřít tunel ke koncovým bodům Swarmu, spusťte příkaz podobný tomuto:

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```
> [!NOTE]
> Ujistěte se, že se na port 2375 neváže žádný jiný místní proces. Pokud například místně spouštíte démona Dockeru, ve výchozím nastavení používá port 2375. V případě potřeby můžete zadat jiný místní port než 2375.
>

Nyní můžete přistupovat ke clusteru Docker Swarm pomocí rozhraní příkazového řádku Dockeru (Docker CLI) v místním systému. Pokyny k instalaci najdete v tématu [Instalace Dockeru](https://docs.docker.com/engine/installation/).

Nastavte proměnnou prostředí DOCKER_HOST na místní port, který jste nakonfigurovali pro tunel. 

```bash
export DOCKER_HOST=:2375
```

Spusťte příkazy Dockeru, které vytvoří tunel ke clusteru Docker Swarm. Příklad:

```bash
docker info
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Vytvoření tunelu SSH ve Windows
Tunely SSH je ve Windows možné vytvořit několika způsoby. Pokud používáte Bash on Ubuntu on Windows nebo podobný nástroj, můžete postupovat podle pokynů k tunelování SSH popsaných výše v tomto článku pro macOS a Linux. Tato část popisuje, jak ve Windows alternativně vytvořit tunel pomocí PuTTY.

1. [Stáhněte si PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) do svého počítače s Windows.

2. Spusťte aplikaci.

3. Zadejte název hostitele, který se skládá z uživatelského jména správce clusteru a veřejného názvu DNS prvního hlavního serveru v clusteru. **Název hostitele** vypadá podobně jako `azureuser@PublicDNSName`. Jako **port** zadejte 2200.

    ![Konfigurace PuTTY 1](./media/container-service-connect/putty1.png)

4. Vyberte **SSH > Ověřování**. Pro ověření přidejte cestu ke svému souboru privátního klíče (formát .ppk). Můžete použít nástroj typu [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) k vygenerování tohoto souboru z klíč SSH použitého při vytváření clusteru.

    ![Konfigurace PuTTY 2](./media/container-service-connect/putty2.png)

5. Vyberte **SSH > Tunely** a nakonfigurujte následující přesměrované porty:

    * **Zdrojový port:** Použijte 80 pro DC/OS nebo 2375 pro Swarm.
    * **Cíl:** Použijte localhost:80 pro DC/OS nebo localhost:2375 pro Swarm.

    Následující příklad je nakonfigurován pro DC/OS, ale pro Docker Swarm bude vypadat obdobně.

    > [!NOTE]
    > Při vytváření tohoto tunelu se port 80 nesmí používat.
    > 

    ![Konfigurace PuTTY 3](./media/container-service-connect/putty3.png)

6. Po dokončení uložte konfiguraci připojení kliknutím na **Relace > Uložit**.

7. K relaci PuTTY se připojíte kliknutím na **Otevřít**. Po připojení je možné v protokolu událostí PuTTY vidět konfiguraci portů.

    ![Protokol událostí PuTTY](./media/container-service-connect/putty4.png)

Až bude tunel pro DC/OS nakonfigurovaný, budete mít k příslušným koncovým bodům přístup přes tyto adresy:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Po dokončení konfigurace tunelu pro Docker Swarm otevřete nastavení systému Windows a nastavte proměnnou prostředí `DOCKER_HOST` na hodnotu `:2375`. Pak budete mít přístup ke clusteru Swarm přes příkazový řádek Dockeru.

## <a name="next-steps"></a>Další postup
Nasazení a správa kontejnerů ve vašem clusteru:

* [Práce s Azure Container Service a Kubernetes](../articles/container-service/kubernetes/container-service-kubernetes-ui.md)
* [Práce se službou Azure Container Service a DC/OS](../articles/container-service//dcos-swarm/container-service-mesos-marathon-rest.md)
* [Práce s Azure Container Service a Docker Swarm](../articles//container-service/dcos-swarm/container-service-docker-swarm.md)

