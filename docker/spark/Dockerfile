FROM bitnami/spark:3.5.0

USER root
RUN pip install --no-cache-dir \
      numpy==1.24.4 \
      pandas==2.2.2 \
      pyarrow==15.0.2
# (Optional) häufig genutzte ML/ETL-Pakete:
# RUN pip install --no-cache-dir scikit-learn==1.4.2

USER 1001
