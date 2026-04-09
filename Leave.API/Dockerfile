# Use .NET runtime image
FROM mcr.microsoft.com/dotnet/aspnet:8.0

# Set working directory
WORKDIR /app

# Copy all files
COPY . .
ENV ASPNETCORE_URLS=http://+:5005
# Expose port (change per API later)
EXPOSE 5005

# Run app
ENTRYPOINT ["dotnet", "Leave.API.dll"]
