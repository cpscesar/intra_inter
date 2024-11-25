import math
import pandas as pd
from sentence_transformers import SentenceTransformer
from sklearn.cluster import AgglomerativeClustering
import subprocess

# Example synthetic dataset
data = {
    'Intra/Interpersonal': [
        'Risk Perception',
        'Trust in neighbors to provide help during disasters.',
        'Confidence in own ability to create an emergency kit.',
        'Willingness to evacuate when instructed.',
        float('nan'),
        'Participation in community drills for disaster preparedness.',
    ]
}
df = pd.DataFrame(data)

# Generating titles/themes using Ollama's Llama:3.1
titles = []

for z in df['Intra/Interpersonal']:
    print(f"Processing: {z}")

    if isinstance(z, float) and math.isnan(z):  # Handle NaN values
        titles.append(float('nan'))
    else:
        # Prompt for disaster preparedness determinants
        template = """Q: Provide a short/concise title for this determinant of disaster preparedness behavior (write just the title): {content}
        Answer:"""
        formatted_prompt = template.format(content=z)

        try:
            # Query Ollama Llama using subprocess.run
            result = subprocess.run(
                ['ollama', 'run', 'llama3.1:latest'],
                input=formatted_prompt,
                capture_output=True,
                text=True,
                check=True
            )
            output = result.stdout.strip()
            titles.append(output)
            print(f"Generated Title: {output}")
        except subprocess.CalledProcessError as e:
            print(f"Error generating title: {e}")
            titles.append("Error generating title")

# Add generated titles to DataFrame
df['titles1'] = titles

# Transforming titles into numeric vectors, generating clusters, and adding the result to the DataFrame
# Using SentenceTransformer for embedding generation
df['titles1'] = df['titles1'].astype(str)
model = SentenceTransformer('bert-base-nli-mean-tokens')  # Pre-trained model for embeddings
note_embeddings = model.encode(df['titles1'])

# Clustering based on embeddings
clustering = AgglomerativeClustering(n_clusters=None, distance_threshold=15, linkage='average')
clusters = clustering.fit_predict(note_embeddings)

df['Cluster_titles1'] = clusters

# Save to Excel
output_file = 'disaster_preparedness_clusters.xlsx'
df.to_excel(output_file, index=False)
print(f"Processed data saved to {output_file}")
