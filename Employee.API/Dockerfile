# Use .NET runtime image
FROM mcr.microsoft.com/dotnet/aspnet:8.0

# Set working directory
WORKDIR /app

# Copy all files
COPY . .

ENV ASPNETCORE_URLS=http://+:5003

EXPOSE 5003

# Run app
ENTRYPOINT ["dotnet", "Employee.API.dll"]
