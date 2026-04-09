# Use .NET runtime image
FROM mcr.microsoft.com/dotnet/aspnet:8.0

# Set working directory
WORKDIR /app

# Copy all files
COPY . .

# Expose port (change per API later)
EXPOSE 5000

# Run app
ENTRYPOINT ["dotnet", "ApiGateway.dll"]
