# Use .NET runtime image
FROM mcr.microsoft.com/dotnet/aspnet:8.0

# Set working directory
WORKDIR /app

# Copy all files
COPY . .
ENV ASPNETCORE_URLS=http://+:5006
# Expose port (change per API later)
EXPOSE 5006

# Run app
ENTRYPOINT ["dotnet", "MasterData.API.dll"]
