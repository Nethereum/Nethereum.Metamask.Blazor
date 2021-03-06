# Nethereum.Metamask.Blazor
Metamask + Nethereum + Blazor interop
![Metamask](screenshots/quickdemo.gif "Metamask Blazor Netehreum")
Template
![Metamask](screenshots/demoErc20.gif "Metamask Blazor Netehreum")

```csharp
@page "/"
@inject IJSRuntime jsRuntime;
@inject MetamaskHostProvider _metamaskHostProvider;
@inject NethereumAuthenticator  _nethereumAuthenticator;
@using Nethereum.Web3;
@using Nethereum.Hex.HexTypes;
<br />
Selected Account: @SelectedAccount
<br />
<button @onclick="@GetBlockHashAsync">Get BlockHash</button>
<br />
@BlockHash
<br />
<button @onclick="@TransferEtherAsync">Transfer Ether</button>
<br />
@TransactionHash
@ErrorTransferMessage


<button @onclick="@AuthenticateAsync">Authenticate</button>
@AuthenticatedAccount


@code{

    bool MetamaskAvailable { get; set; }
    string SelectedAccount { get; set; }
    string BlockHash { get; set; }
    string TransactionHash { get; set; }
    string ErrorTransferMessage { get; set; }
    protected string AuthenticatedAccount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        _metamaskHostProvider.SelectedAccountChanged += MetamaskHostProvider_SelectedAccountChanged;
        MetamaskAvailable = await _metamaskHostProvider.CheckProviderAvailabilityAsync();
    }


    private async Task MetamaskHostProvider_SelectedAccountChanged(string account)
    {
        SelectedAccount = account;
        this.StateHasChanged();
    }


    protected async Task GetBlockHashAsync()
    {
        var web3 = await _metamaskHostProvider.GetWeb3Async();
        var block = await web3.Eth.Blocks.GetBlockWithTransactionsByNumber.SendRequestAsync(new HexBigInteger(1));
        BlockHash = block.BlockHash;
    }

    protected async Task TransferEtherAsync()
    {
        try
        {
            var web3 = await _metamaskHostProvider.GetWeb3Async();

            TransactionHash = await web3.Eth.GetEtherTransferService().TransferEtherAsync("0x13f022d72158410433cbd66f5dd8bf6d2d129924", 0.001m);
        }
        catch (Exception ex)
        {
            ErrorTransferMessage = ex.Message;
        }
    }

    public async Task AuthenticateAsync()
    {

        AuthenticatedAccount = await _nethereumAuthenticator.RequestNewChallengeSignatureAndRecoverAccountAsync();

    }
}

```
