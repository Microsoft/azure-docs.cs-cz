Po [nasazení clusteru Azure Container Service](../articles/container-service/dcos-swarm/container-service-deployment.md) možná budete potřebovat změnit počet uzlů agentů. Například můžete potřebovat přidat více agentů, abyste mohli spouštět více instancí nebo aplikací typu kontejner. 

Počet uzlů agentů v clusteru DC/OS, Docker Swarm nebo Kubernetes můžete změnit pomocí webu Azure portal nebo rozhraní příkazového řádku Azure. 

## <a name="scale-with-the-azure-portal"></a>Škálování pomocí webu Azure Portal

1. Na webu [Azure Portal](https://portal.azure.com) přejděte na **Služby kontejneru** a klikněte na službu kontejneru, kterou chcete upravit.
2. V okně **Služba kontejneru** klikněte na **Agenti**.
3. Do pole **Počet virtuálních počítačů** zadejte požadovaný počet uzlů agentů.

    ![Škálování fondu na portálu](./media/container-service-scale/container-service-scale-portal.png)

4. Konfiguraci uložíte kliknutím na **Uložit**.

## <a name="scale-with-the-azure-cli"></a>Škálování pomocí Azure CLI

Ujistěte se, že jste [nainstalované](/cli/azure/install-az-cli2) nejnovější rozhraní příkazového řádku Azure a přihlášení k účtu Azure (`az login`).

### <a name="see-the-current-agent-count"></a>Zobrazení aktuálního počtu agentů
Pokud chcete zobrazit aktuální počet agentů v clusteru, spusťte příkaz `az acs show`. Zobrazí se konfigurace clusteru. Například následující příkaz zobrazí konfiguraci služby kontejneru `containerservice-myACSName` ve skupině prostředků `myResourceGroup`:

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

Příkaz vrátí počet agentů v hodnotě `Count` v části `AgentPoolProfiles`.

### <a name="use-the-az-acs-scale-command"></a>Použití příkazu az acs scale
Pokud chcete změnit počet uzlů agentů, spusťte příkaz `az acs scale` a zadejte **skupinu prostředků**, **název služby kontejneru** a požadovaný **nový počet agentů**. Použitím nižšího nebo vyššího čísla můžete vertikálně snížit nebo navýšit kapacitu.

Pokud například chcete změnit počet agentů v předchozím clusteru na 10, zadejte následující příkaz:

```azurecli
az acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

Azure CLI vrátí řetězec JSON představující novou konfiguraci služby kontejneru, včetně nového počtu agentů.

Další možnosti příkazu zobrazíte spuštěním příkazu `az acs scale --help`.

## <a name="scaling-considerations"></a>Důležité informace o škálování

* Počet uzlů agentů musí být mezi 1 a 100 včetně. 

* Vaše kvóta pro jádra může omezovat počet uzlů agentů v clusteru.

* Operace škálování uzlů agentů se používají na škálovací sadu virtuálních počítačů Azure, která obsahuje fond agentů. V clusteru DC/OS se operacemi ukázanými v tomto článku škálují pouze uzly agentů v privátním fondu.

* V závislosti na orchestrátoru, který v clusteru nasadíte, můžete samostatně škálovat počet instancí kontejneru spuštěného v clusteru. Například v clusteru DC/OS můžete změnit počet instancí aplikace typu kontejner pomocí [uživatelského rozhraní Marathon](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md).


## <a name="next-steps"></a>Další postup
* Zobrazit [Další příklady](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) pomocí příkazů Azure CLI s Azure Container Service.
* Další informace o [fondech agentů DC/OS](../articles/container-service/dcos-swarm/container-service-dcos-agents.md) ve službě Azure Container Service.

