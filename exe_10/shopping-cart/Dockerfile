FROM python:3.9-slim

WORKDIR /app

COPY shopping_cart.py .

RUN pip install --no-cache-dir flask

EXPOSE 80

CMD ["python", "shopping_cart.py"]