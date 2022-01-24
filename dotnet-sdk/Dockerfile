FROM mcr.microsoft.com/dotnet/sdk:latest
#RUN export DOTNET_TieredPGO=1 # This enables dynamic (tiered) PGO for .Net 6 and above.
#RUN export DOTNET_ReadyToRun=0 # This will cause the core .Net libraries to be part of tiered PGO.
#RUN export DOTNET_TC_QuickJitForLoops=1 # Enables tiering for methods that contain loops.
    # Setting these three variables is known as "full PGO."
EXPOSE 5000
EXPOSE 5001
EXPOSE 80
EXPOSE 443
